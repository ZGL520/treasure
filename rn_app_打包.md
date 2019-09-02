---
title: rn-app-打包
tags: react-native
date: 2019/8/27
categories: react-native
---


在安卓端APP构建前需要：

1，添加签名

签名文件路径如下

    \app_2.3.0_beta\android\app\release-key.keystore


签名名字密码在如下文件

    \app_2.3.0_beta\android\app\build.gradle


2，编译

编译会编译到如下路劲：

安卓命令

    react-native bundle --entry-file index.android.js --bundle-output ./android/app/src/main/assets/index.android.bundle --platform android --assets-dest ./android/app/src/main/res/ --dev false



编译到如下路劲

    /app_2.3.0_beta/android/app/src/main/assets/

一共两个文件，文件名：

    index.android.bundle

    index.android.bundle.meta


ps:调试时需要打开服务，如下命令

    npm start  -- --reset-cache


