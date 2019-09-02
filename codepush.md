---
title: codepush
tags: react-native
date: 2019/09/01
categories: react-native
---

codepush 用来热更新 app 里 js bundle 那一部份的内容
详细的英文  文档
[https://docs.microsoft.com/en-us/appcenter/distribution/codepush/](https://docs.microsoft.com/en-us/appcenter/distribution/codepush/)

 要使用 codepush 热更新，需要做三件事情。

第一：微软的 app center
使用微软的 [app center](https://appcenter.ms/apps) 服务。热更新的服务包括在里面。当 app  咨询是否有更新的时候，它是  咨询这里面的配置；当我们发布新的热更新 js bundle 也是发布到这里。

需要安装它的命令行工具 `npm install -g appcenter-cli` appcenter 命令行工具详细的命令说明看[这里](https://docs.microsoft.com/en-us/appcenter/distribution/codepush/cli)

安装完成后，登陆一下 `appcenter login`，也可以登陆 [https://appcenter.ms/](https://appcenter.ms/) 熟悉一下里面的 codepush 服务

**注意**
需要使用 appcenter 网站 或者命令行工具 创建 appcenter 里的 app（就是一个名字，包含一个或多个 deployment 名字，比如：Staging, Production），我们在发布热更新的时候，可以指定发布到哪个 deployment。

第二：配置 app 项目的代码使用 codepush 功能 [详细](https://docs.microsoft.com/en-us/appcenter/distribution/codepush/react-native#getting-started)

#### 2.1 安装 `npm install --save react-native-code-push`

#### 2.2 link `react-native link react-native-code-push` 或者手动配置

#### 2.3 [使用](https://docs.microsoft.com/en-us/appcenter/distribution/codepush/react-native#plugin-usage)

用 codePush 包装 root component，默认是 app 打开的时候检查是否需要更新。如果需要设置其它东西，可以使用配置项。

```jsx
import codePush from 'react-native-code-push'

class MyApp extends Component {}

MyApp = codePush(MyApp)
```

第三：运行版本更新的命令就可以了，运行这个命令会打包新的 js Bundle，自动上传到 app center，app 就会提示更新。默认是发布到 Staging。

```
appcenter codepush release-react -a <ownerName>/<appName>
appcenter codepush release-react -a <ownerName>/MyApp-iOS
appcenter codepush release-react -a <ownerName>/MyApp-Android
```

然后，验证没问题之后，可以[这样](https://docs.microsoft.com/en-us/appcenter/distribution/codepush/cli#promoting-updates)发布到正式环境

```
appcenter codepush promote -a <ownerName>/<appName> -s <sourceDeploymentName> -d <destDeploymentName>
appcenter codepush promote -a <ownerName>/<appName> -s Staging -d Production
```

	appcenter codepush release-react -a name //发布命令
	
	appcenter codepush deployment list -a name //查看app key
	
	安卓key配置位置
	android/src/main/java/com/seatour/MainApplication.java  //67行

