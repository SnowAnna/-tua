### 本文是基于最新的react-navigation```^1.0.0-beta.13```来总结的。

#### 1.为啥使用react-navigation？

- react-navigation比Navigator更加流畅，体验要好一些；
- 编码更规范，有层次感，易于管理维护；
- 官方推荐，没有发现明显的bug，且用户量大，社区活跃，文档齐全。

#### 2.参考链接

- [官方文档](https://reactnavigation.org/docs/navigators/tab)
- [react-navigation使用技巧](http://www.jianshu.com/p/2f575cc35780)
- [react-navigation使用技巧(进阶篇)](http://www.jianshu.com/p/b877115fff1b)
- [一个用react-navigation写的较大型的app，结合redux，本文的参考上线项目](https://github.com/jaggerwang/zqc-app-demo)
- [前端大神写的，必看!](http://www.jianshu.com/p/bf30b66bbd58)

#### 3.功能

- StackNavigator
  类似顶部导航条，用来跳转页面和传递参数。
  
![image.png](http://upload-images.jianshu.io/upload_images/5339345-6dc8f6cb91fd57d7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/240)
- TabNavigator
  类似底部标签栏，用来区分模块。
  
![image.png](http://upload-images.jianshu.io/upload_images/5339345-9355977268cb69bc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/240)
- DrawerNavigator
抽屉，类似从App左侧滑出一个页面，**实际实用性不大**，我们一般用到的是[DrawerLayoutAndroid](http://reactnative.cn/docs/0.48/drawerlayoutandroid.html#content)或是其他开源组件，所以在这里不做讲解。

![20160708152335923.png](http://upload-images.jianshu.io/upload_images/5339345-1c4c07805bf26dc3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/240)

#### 4.相关api及基本用法

> 看文档或者其他介绍文档文章

#### 5.最佳实践

###### 以“在球场app”为例，它把所有的导航都封装在了一个文件中（navigation.js）:

```javascript
/**
 * 在球场
 * zaiqiuchang.com
 */

import React from 'react'
import {StackNavigator, TabNavigator} from 'react-navigation'

import {COLOR} from './config'
import * as components from './components'

const login = StackNavigator(
  {
    PreLogin: {screen: components.PreLogin},
    Login: {screen: components.Login},
    ResetPassword: {screen: components.ResetPassword},
    RegisterMobile: {screen: components.RegisterMobile},
    RegisterVerify: {screen: components.RegisterVerify},
    RegisterProfile: {screen: components.RegisterProfile}
  },
  {
    navigationOptions: {
      headerTintColor: COLOR.textLightNormal,
      headerStyle: {backgroundColor: COLOR.theme}
    }
  }
)

const tabNearBy = StackNavigator(
  {
    Nearby: {screen: components.Nearby},
    SelectCityAndSport: {screen: components.SelectCityAndSport},
    Album: {screen: components.Album},
    Player: {screen: components.Player}
  },
  {
    navigationOptions: {
      headerTintColor: COLOR.textLightNormal,
      headerStyle: {backgroundColor: COLOR.theme},
      tabBarIcon: ({focused, tintColor}) => (
        <components.Icon
          name='location-on'
          style={{color: tintColor, fontSize: 20}}
        />
      ),
      title: '附近'
    }
  }
)

const tabAtCourt = StackNavigator(
  {
    AtCourt: {screen: components.AtCourt}
  },
  {
    navigationOptions: {
      headerTintColor: COLOR.textLightNormal,
      headerStyle: {backgroundColor: COLOR.theme},
      tabBarIcon: ({focused, tintColor}) => (
        <components.Icon
          name='add-box'
          style={{color: tintColor, fontSize: 20}}
        />
      ),
      title: '在球场'
    }
  }
)

const tabMe = StackNavigator(
  {
    Me: {screen: components.Me},
    EditProfileGender: {screen: components.EditProfileGender},
    EditProfile: {screen: components.EditProfile},
    EditProfileNickname: {screen: components.EditProfileNickname},
    EditProfileAvatar: {screen: components.EditProfileAvatar},
    EditProfileEmail: {screen: components.EditProfileEmail},
    EditProfileIntro: {screen: components.EditProfileIntro},
    EditProfileBackground: {screen: components.EditProfileBackground},
    Settings: {screen: components.Settings},
    SelectRate: {screen: components.SelectRate},
    About: {screen: components.About}
  },
  {
    navigationOptions: {
      headerTintColor: COLOR.textLightNormal,
      headerStyle: {backgroundColor: COLOR.theme},
      tabBarIcon: ({focused, tintColor}) => (
        <components.Icon
          name='account-circle'
          style={{color: tintColor, fontSize: 20}}
        />
      ),
      title: '我'
    }
  }
)

const tab = TabNavigator(
  {
    TabNearBy: {screen: tabNearBy},
    TabAtCourt: {screen: tabAtCourt},
    TabMe: {screen: tabMe}
  },
  {
    tabBarPosition: 'bottom',
    lazy: true,
    tabBarOptions: {
      activeTintColor: COLOR.theme,
      activeBackgroundColor: COLOR.backgroundLighter,
      inactiveTintColor: COLOR.textEmpha,
      inactiveBackgroundColor: COLOR.backgroundDarker,
      showIcon: true,
      showLabel: true,
      style: {backgroundColor: COLOR.backgroundDarker},
      tabStyle: {paddingTop: 2, paddingBottom: 0},
      labelStyle: {fontSize: 12, marginTop: 0, marginBottom: 5}
    },
    headerMode: 'none'
  }
)

export const AppNavigator = StackNavigator(
  {
    Bootstrap: {screen: components.Bootstrap},

    Login: {screen: login},

    Tab: {screen: tab}
  },
  {
    initialRouteName: 'Bootstrap',
    headerMode: 'none',
    navigationOptions: {
      gesturesEnabled: false
    }
  }
)

export function navToBootstrap (navigation, {isReset = false} = {}) {
  navigation.navigate('Bootstrap', {isReset})
}

export function navToTab (navigation) {
  navigation.navigate('Tab')
}

export function navToAlbum (navigation, {files, currentIndex = 0}) {
  navigation.navigate('Album', {files, currentIndex})
}

export function navToPlayer (navigation, {file, autoPlay}) {
  navigation.navigate('Player', {file, autoPlay})
}

```

###### 分析结构：

- 1.下面四个方法暂且不看，大概就是传递各种参数，参考以上各种文档连接；

- 2.导出的是AppNavigator方法，使用了react-navigation的StackNavigator方法，其中很容易看到一共分为了三个大部分Bootstrap, Login, Tab：

```
{
    Bootstrap: {screen: components.Bootstrap},

    Login: {screen: login},

    Tab: {screen: tab}
}
```

> Bootstrap是启动页

> Login是登录部分导航合集

> Tab是内容部分导航架构合集

###### 一、其中Bootstrap比较简单，负责app启动部分展示及交互；

###### 二、分析Login部分：

```javascript
const login = StackNavigator(
  {
    PreLogin: {screen: components.PreLogin},
    Login: {screen: components.Login},
    ResetPassword: {screen: components.ResetPassword},
    RegisterMobile: {screen: components.RegisterMobile},
    RegisterVerify: {screen: components.RegisterVerify},
    RegisterProfile: {screen: components.RegisterProfile}
  },
  {
    navigationOptions: {
      headerTintColor: COLOR.textLightNormal,
      headerStyle: {backgroundColor: COLOR.theme}
    }
  }
)
```

1.首先，使用了StackNavigator功能，就是正常的页面跳转传参；

2.其中包含六个页面，都是和登录校验相关的比如PreLogin（登陆前）、Login（登录）、ResetPassword（重设密码）等页面。

3.登录部分单独的配置navigationOptions；

###### 三、Tab部分（app的主体部分）

根据```Tab: {screen: tab}```源码，Tab也是个合集，来看tab是怎样被封装的：

```javascript
const tab = TabNavigator(
{
    TabNearBy: {screen: tabNearBy},
    TabAtCourt: {screen: tabAtCourt},
    TabMe: {screen: tabMe}
  },
  {
    tabBarPosition: 'bottom',
    lazy: true,
    tabBarOptions: {
      activeTintColor: COLOR.theme,
      activeBackgroundColor: COLOR.backgroundLighter,
      inactiveTintColor: COLOR.textEmpha,
      inactiveBackgroundColor: COLOR.backgroundDarker,
      showIcon: true,
      showLabel: true,
      style: {backgroundColor: COLOR.backgroundDarker},
      tabStyle: {paddingTop: 2, paddingBottom: 0},
      labelStyle: {fontSize: 12, marginTop: 0, marginBottom: 5}
    },
    headerMode: 'none'
 }
)
```
显然，使用了React Navigation的TabNavigator功能；
其中，包含三部分TabNearBy、TabAtCourt、TabMe，也就是如下图三部分tab标签（附近、在球场、我）：

![Screenshot_2017-10-09-18-20-23-410_在球场.png](http://upload-images.jianshu.io/upload_images/5339345-f9176a0c812d2f75.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/240) 







- **TabNearBy （附近）**

```javascript
TabNearBy: {screen: tabNearBy}
```

同样被封装，来看tabNearBy的封装代码：

```javascript
const tabNearBy = StackNavigator(
  {
    Nearby: {screen: components.Nearby},
    SelectCityAndSport: {screen: components.SelectCityAndSport},
    Album: {screen: components.Album},
    Player: {screen: components.Player}
  },
  {
    navigationOptions: {
      headerTintColor: COLOR.textLightNormal,
      headerStyle: {backgroundColor: COLOR.theme},
      tabBarIcon: ({focused, tintColor}) => (
        <components.Icon
          name='location-on'
          style={{color: tintColor, fontSize: 20}}
        />
      ),
      title: '附近'
    }
  }
)
```

包含四部分导航：

> 1.Nearby  （附近，主题页面）

![Screenshot_2017-10-09-18-20-23-410_在球场.png](http://upload-images.jianshu.io/upload_images/5339345-f9176a0c812d2f75.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/240) 

> 2.SelectCityAndSport （选择城市，作者把选择城市的弹框当成一层页面来用，解决了物理返回键的问题，很得体。）

![Screenshot_2017-10-10-10-58-37-675_在球场.png](http://upload-images.jianshu.io/upload_images/5339345-e758413743ebb9c7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/240)

> 3.Album （相册部分）

![Screenshot_2017-10-10-11-18-12-994_在球场.png](http://upload-images.jianshu.io/upload_images/5339345-cd0fb3f9c95c57e3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/240)

> 4.Player （个人主页）

![Screenshot_2017-10-10-11-17-15-622_在球场.png](http://upload-images.jianshu.io/upload_images/5339345-92707dc75ca1d1a5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/240)

**实际上就是“附近”模块下面的页面的导航合集。**


- **tabAtCourt （在球场）**

![Screenshot_2017-10-10-11-19-24-642_在球场.png](http://upload-images.jianshu.io/upload_images/5339345-475ca58297c48bbd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/240)

查看源码：

```javascript
const tabAtCourt = StackNavigator(
  {
    AtCourt: {screen: components.AtCourt}
  },
  {
    navigationOptions: {
      headerTintColor: COLOR.textLightNormal,
      headerStyle: {backgroundColor: COLOR.theme},
      tabBarIcon: ({focused, tintColor}) => (
        <components.Icon
          name='add-box'
          style={{color: tintColor, fontSize: 20}}
        />
      ),
      title: '在球场'
    }
  }
)
```

相对比较简单，只有一个模块AtCourt。

- **tabMe （我）**

```javascript
const tabMe = StackNavigator(
  {
    Me: {screen: components.Me},
    EditProfileGender: {screen: components.EditProfileGender},
    EditProfile: {screen: components.EditProfile},
    EditProfileNickname: {screen: components.EditProfileNickname},
    EditProfileAvatar: {screen: components.EditProfileAvatar},
    EditProfileEmail: {screen: components.EditProfileEmail},
    EditProfileIntro: {screen: components.EditProfileIntro},
    EditProfileBackground: {screen: components.EditProfileBackground},
    Settings: {screen: components.Settings},
    SelectRate: {screen: components.SelectRate},
    About: {screen: components.About}
  },
  {
    navigationOptions: {
      headerTintColor: COLOR.textLightNormal,
      headerStyle: {backgroundColor: COLOR.theme},
      tabBarIcon: ({focused, tintColor}) => (
        <components.Icon
          name='account-circle'
          style={{color: tintColor, fontSize: 20}}
        />
      ),
      title: '我'
    }
  }
)
```

关于我这个模块相对比较复杂，但是主要是一些编辑个人信息，设置，个人主页等页面。

![Screenshot_2017-10-10-10-53-33-765_在球场.png](http://upload-images.jianshu.io/upload_images/5339345-70582209e8b63bb8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/240)  ![Screenshot_2017-10-10-10-55-58-870_在球场.png](http://upload-images.jianshu.io/upload_images/5339345-75b4d01c823f66ac.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/240) ![Screenshot_2017-10-10-10-57-02-276_在球场.png](http://upload-images.jianshu.io/upload_images/5339345-71a3a5887863dd36.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/240)


### 至此，“在球场”整体导航设计已经出来了，接下来就是具体页面的个性化设置，比如显示标题、传参、跳转等。任选其中一个页面进行分析：

查看Nearby组件：

```javascript
static navigationOptions = ({navigation}) => {
    let {cityAndSport, onPressCityAndSport} = navigation.state.params || {}
    let button = cityAndSport && onPressCityAndSport
      ? <components.NavButton onPress={onPressCityAndSport}>
        {cityAndSport}</components.NavButton>
      : null
    let navigationOptions = {
      title: '附近'
    }
    if (Platform.OS === 'ios') {
      navigationOptions.headerLeft = button
    } else {
      navigationOptions.headerRight = button
    }
    return navigationOptions
 };
```

> 就是根据传过来的参数cityAndSport和onPressCityAndSport来判断显示的button状态。注意这种方式编码的话，要把navigationOptions返回出去。实际上，navigationOptions的写法灵活多变，具体技巧在最上边的参考链接里有介绍。

