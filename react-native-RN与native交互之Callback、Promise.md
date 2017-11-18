# 《React-Native系列》3、RN与native交互之Callback、Promise


接着上一篇 React-Native系列 2、RN与native交互与数据传递 ，我们接下来研究另外的两种RN与Native交互的机制
## 一、Callback机制
首先Calllback是异步的，RN端调用Native端，Native会callback，但是时机是不确定的，如果多次调用的话，会存在问题。
Naive端是无法主动通过回调函数向RN端发送消息的。
具体实现代码如下：
Native端暴露好接口
```java
[java] view plain copy
@ReactMethod  
public void measureLayout(Callback errorCallback,  
                          Callback successCallback){  
    try {  
        successCallback.invoke(100, 100, 200, 200);//调用回调函数，返回结果  
    } catch (IllegalViewOperationException e) {  
        errorCallback.invoke(e.getMessage());  
    }  
}  
在RN端：
[javascript] view plain copy
<Text style={{ fontSize: 25 }} onPress={this.CallAndroid_callback} >调用原生方法_使用_回调函数</Text>  
  CallAndroid_callback = () => {  
    NativeModules.MyNativeModule.measureLayout(  
      (msg) => {  
        console.log(msg);  
      },  
      (x, y, width, height) => {  
        console.log(x + '坐标,' + y + '坐标,' + width + '宽,' + height + '高');  
      }  
    );  
  }  
```
## 二、promise机制
关于ES6中Promise的用法可以参考：http://www.cnblogs.com/lvdabao/p/5320705.html
Promise 的状态
一个 Promise 的当前状态必须为以下三种状态中的一种：等待态（Pending）、完成态（Fulfilled）和拒绝态（Rejected）。
等待态（Pending）
处于等待态时，promise 需满足以下条件：

可以迁移至完成态或拒绝态
 
完成态（Fulfilled）
处于完成态时，promise 需满足以下条件：
不能迁移至其他任何状态
必须拥有一个不可变的终值
拒绝态（Rejected）
处于拒绝态时，promise 需满足以下条件：

不能迁移至其他任何状态
必须拥有一个不可变的据因

在Native侧，暴露接口：
```java
[java] view plain copy
@ReactMethod  
public void rnCallNative_promise(String msg, Promise promise) {  
  
    try {  
        //业务逻辑处理  
        Toast.makeText(mContext, msg, Toast.LENGTH_SHORT).show();  
        String componentName = getCurrentActivity().getComponentName().toString();  
        promise.resolve(componentName);  
    }catch (Exception e){  
        promise.reject("100",e.getMessage());//promise 失败  
    }  
}  

在RN侧：
[javascript] view plain copy
<Text style={{ fontSize: 25 }} onPress={this.CallAndroid_promise} >调用原生方法_使用_Promise</Text>  
  
  CallAndroid_promise = () => {  
    NativeModules.MyNativeModule.rnCallNative_promise('rn调用原生模块的方法-成功啦').then(  
      (msg) => {  
        console.log('promise成功：'+msg);  
      }  
    ).catch(  
      (err) => {  
        console.log(err);  
      }  
      );  
  }  
}  
```