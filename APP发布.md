## rn app 产品发布


###Android

1，修改版本号，项目内

   修改路径

    android/app/build.gradle //路径

    //代码

    versionCode 4  //每次发布加1，发布平台用于检测APP更新，提醒用户
    versionName "2.3.0"  //APP版本号

2，修改环境配置，项目内

   修改路径

    app/utils/config.js //将环境切换到发布环境，测试环境注释掉，发布之后将环境改到测试环境，通常给测试只需要打包测试版本app

3，编译

   编译命令，项目根目录下执行

    react-native bundle --entry-file index.android.js --bundle-output ./android/app/src/main/assets/index.android.bundle --platform android --assets-dest ./android/app/src/main/res/ --dev false

3，Android studio -> build -> clean project

4，签名

   签名文件路径如下

    android\app\release-key.keystore

   操作

   Android studio -> build -> generate signed apk -> [填入签名文件路径，key store password,名字，密码] -> next-> [选择构建类型，resolve or debug ] -> finish


   签名名字密码在如下文件配置

    android\app\build.gradle

5，将打包的APP发给相关人员

6，发布需要在后台上传APP


---
###iOS


1，开发者账号创建APP项目

2，xcode 修改版本号

3，检查项目内的配置文件，版本号

4，打包操作

   xcode -> build -> archive

5，打包成功后导出或者第六步直接上传

  a，APP store 上传：APP store

  b，测试：ad Hoc

6，打包成功后直接上传

   操作：一路默认

7，上传成功后去开发者账号，检查审核信息，包括版本号、icon图标等，然后选择相应的版本提交审核，提交审核后有一个问题需要选择，现在用不到提示的功能所以全部选‘否’。然后完成等待审核，审核进度会在公司邮箱通知，或者在登录开发者账号查询

---

##ps:每次发布成功后，主干都得打个tags