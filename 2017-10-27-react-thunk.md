# redux-thunk作用
redux-thunk 是一个比较流行的 redux 异步 action 中间件，比如 action 中有 setTimeout 或者通过 fetch通用远程 API 这些场景，那么久应该使用 redux-thunk 了。redux-thunk 帮助你统一了异步和同步 action 的调用方式，把异步过程放在 action 级别解决，对 component 没有影响。下面通过例子一步步来看看。

作者：RN学习
链接：http://www.jianshu.com/p/8dc309a8b4f7
來源：简书
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。

## 异步方法的调用
```java
store.dispatch({ type: 'SHOW_NOTIFICATION', text: 'You logged in.' })
setTimeout(() => {
  store.dispatch({ type: 'HIDE_NOTIFICATION' })
}, 5000)

这是一个简单的例子，他做事情很简单，5s 后关闭提醒。
```

在一个被 redux connect 过的 component 中，是如下这个样子：
``java
this.props.dispatch({ type: 'SHOW_NOTIFICATION', text: 'You logged in.' })
setTimeout(() => {
  this.props.dispatch({ type: 'HIDE_NOTIFICATION' })
}, 5000)


```

本质上两者没有区别，只是被 connect 过的 component 中的 this.props 有了 dispatch 属性。

我们为了在不同 component 中重用创建 action 的代码，会把他放到一个 action 的 JS 文件中。
```java
// actions.js
export function showNotification(text) {
  return { type: 'SHOW_NOTIFICATION', text }
}
export function hideNotification() {
  return { type: 'HIDE_NOTIFICATION' }
}

// component.js
import { showNotification, hideNotification } from '../actions'

this.props.dispatch(showNotification('You just logged in.'))
setTimeout(() => {
  this.props.dispatch(hideNotification())
}, 5000)


```

当然，如果我们用了 connect 的第二参数把这两个方法绑定到 component 的 this.props 上，在调用时我们又省了一些代码量，就像这样了：
```java
this.props.showNotification('You just logged in.')
setTimeout(() => {
  this.props.hideNotification()
}, 5000)
```
看起来一切顺利，现在有什么问题呢？

>>在每个使用该功能的 component 中都要写同样的代码。
>>如果两个 notification 时间很接近，当第一个结束了之后，dispatch 了 HIDE_NOTIIFICATION 把第二个也错误的关闭了
```java
// actions.js
function showNotification(id, text) {
  return { type: 'SHOW_NOTIFICATION', id, text }
}
function hideNotification(id) {
  return { type: 'HIDE_NOTIFICATION', id }
}

let nextNotificationId = 0
export function showNotificationWithTimeout(dispatch, text) {
  // Assigning IDs to notifications lets reducer ignore HIDE_NOTIFICATION
  // for the notification that is not currently visible.
  // Alternatively, we could store the interval ID and call
  // clearInterval(), but we’d still want to do it in a single place.
  const id = nextNotificationId++
  dispatch(showNotification(id, text))

  setTimeout(() => {
    dispatch(hideNotification(id))
  }, 5000)
}


```
为了解决上述问题，我们把这部分逻辑也放到了 action ，并引入了 ID 来解决问题2.

我们愉快的用下面的代码调用起来：
```java
// component.js
showNotificationWithTimeout(this.props.dispatch, 'You just logged in.')

// otherComponent.js
showNotificationWithTimeout(this.props.dispatch, 'You just logged out.')


```
我们通过参数把 dispatch传入了进去，实际上一般 component 都会有 this.props.dispatch ，
但是通常为了测试和 mock 方便，还是传入进去比较好一点。
```java
// store.js
export default createStore(reducer)

// actions.js
import store from './store'

// ...

let nextNotificationId = 0
export function showNotificationWithTimeout(text) {
  const id = nextNotificationId++
  store.dispatch(showNotification(id, text))

  setTimeout(() => {
    store.dispatch(hideNotification(id))
  }, 5000)
 }

// component.js
showNotificationWithTimeout('You just logged in.')

// otherComponent.js
showNotificationWithTimeout('You just logged out.')


```
如果你的 store 是全局的也可以这么干，不过这样在 server render 中就不好用了，server render 一般是一个 request 一个 store。

而且一样的对测试和 mock 都不友好。

## 引入 redux-thunk
到目前为止，我们还没有引入 redux-thunk 呢，那我们为什么要用 redux-thunk 呢？如果你是个小应用，那大可不必使用，如果是个大应用，接着往下看，we talk about redux-thunk.

问题在哪？

我们一定要传入 dispatch ，参考 separate container and presentational components ，我们很难在一个 presentational component 中使用 showNotificationWithTimeout() ,因为不一定有 this.props.dispatch。

showNotificationWithTimeout() 不能被 connect 方法绑定到 this.props，因为他不返回一个 redux action.

showNotificationWithTimeout() 仅仅是一个 helper 方法，他和 showNotification 同时存在，很容就用错了。

redux-thunk 怎么做的？

```java
/ actions.js
function showNotification(id, text) {
  return { type: 'SHOW_NOTIFICATION', id, text }
}
function hideNotification(id) {
  return { type: 'HIDE_NOTIFICATION', id }
}

let nextNotificationId = 0
export function showNotificationWithTimeout(text) {
  return function (dispatch) {
    const id = nextNotificationId++
    dispatch(showNotification(id, text))

    setTimeout(() => {
      dispatch(hideNotification(id))
    }, 5000)
  }
}


```
>>如果 redux-thunk 发现 dispatch 了一个函数 dispatch(showNotificationWithTimeout('log in'))，他会传给函数一个dispatch 参数，这解决了 dispatch 不好获取的问题。
>>他会自己「吃掉」这个函数，不会传递给 reduces，防止 reduces 遇到一个函数而不知所措。

我们怎么调用 showNotificationWithTimeout()呢？
```java
// component.js
this.props.dispatch(showNotificationWithTimeout('You just logged in.'))
```
我们可以看到，dispatch 一个异步 action 和 dispatch 一个同步的 action 是一致的，
component 不用关系这个 action 是异步还是同步的，你可以在任何时间修改他。
通过 connect 绑定了这个方法后，完整的例子如下：
```java
// actions.js
    function showNotification(id, text) { 
        return { type: 'SHOW*NOTIFICATION', id, text } 
        } 
    
    
    function hideNotification(id) {
         return { type: 'HIDE*NOTIFICATION', id } 
         }

 let nextNotificationId = 0 export 
 function showNotificationWithTimeout(text) {
      return function (dispatch) { 
          const id = nextNotificationId++ dispatch(showNotification(id, text))

            setTimeout(() => {
            dispatch(hideNotification(id))
                }, 5000)
}}
// component.js

import { connect } from 'react-redux'

// ...

this.props.showNotificationWithTimeout('You just logged in.')

// ...

export default connect( mapStateToProps, { showNotificationWithTimeout } )(MyComponent)


```
## 在 Trunk 中读取状态

有时我们会遇到需要知道当前状态的情况，除了传入 dispatch参数, 
还会把 getState作为第二个参数传入，放个例子感受一下：
```java
let nextNotificationId = 0
export function showNotificationWithTimeout(text) {
  return function (dispatch, getState) {
// Unlike in a regular action creator, we can exit early in a thunk
// Redux doesn’t care about its return value (or lack of it)
if (!getState().areNotificationsEnabled) {
  return
}

const id = nextNotificationId++
dispatch(showNotification(id, text))

setTimeout(() => {
  dispatch(hideNotification(id))
}, 5000)
  }
}


```
一般场景是断路器的场景，如果某个远程调用之前查询一下 cache 等，
如果仅仅是用来区分 dispatch 哪个 action，还是把他放到 reducers 更适合。