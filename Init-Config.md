# 安装配置笔记

记忆力逐渐丧失，只有笔记才能避免原地踏步。

## 一、Flutter + Rust 安装配置

### 1. 安装配置 Rust

* 地址：<https://www.rust-lang.org/zh-CN/tools/install>
* 升级：`$ rustup update`
* 查看：`$ rustup show`

### 2. 安装配置 Flutter

* 地址：<https://flutter.cn/docs/get-started/install>
* 解压：$HOME/Dev/flutter 或者 D:\Dev\flutter
* 将解压路径添加到环境变量PATH中
* 查询版本：`$ flutter --version`
* 升级版本：`$ flutter upgrade`
* 检查配置：`$ flutter doctor`
* 检查位置：`$ where flutter dart`
* 安卓开发：<https://developer.android.google.cn/studio>

## 二、平台相关工作

### 1. 国内网络镜像

* 国内镜像地址，Fuck GFW：<https://flutter.cn/community/china>

  ```shell
  export PUB_HOSTED_URL=https://pub.flutter-io.cn
  export FLUTTER_STORAGE_BASE_URL=https://storage.flutter-io.cn
  ```

* Windows平台开始菜单搜索“环境变量”，添加上述环境变量。
* git 禁止设置代理，否则 `flutter upgrade` 失败

  ```shell
  git config --global --add remote.origin.proxy ""
  git config --global --get remote.origin.proxy
  ```

### 2. macOS 平台

* 需要 .zprofile 导出环境变量，否则 brew 更新困难
  > export HOMEBREW_NO_INSTALL_FROM_API=1
* macOS 与原生 iOS 代码交互
  > sudo gem install cocoapods
* macOS 需要通过AppStore安装 Xcode
* macOS 需要安装 clang
  > xcode-select --install

### 3. Android 平台

* 创建虚拟机图形加速选择：`Hardware - GLES 2.0`
* 安卓虚拟机 “关于” 页面连续点击 “Build number” 开启 “开发者模式”

### 4. Windows 平台

* Android SDK Tools 安装 Google USB Driver

## 三、flutter_rust_bridge 学习笔记

### 1. 安装配置

* 官网：<https://github.com/fzyzcjy/flutter_rust_bridge>
* 文档：<https://cjycode.com/flutter_rust_bridge/index.html>

* 工具和依赖

  > ```shell
  > dart pub global activate ffigen
  > cargo install flutter_rust_bridge_codegen

### 2. 用法

* 转换成 Dart 代码
  > flutter_rust_bridge_codegen --rust-input core/src/api.rs --dart-output ui/lib/api_generated.dart

* 集成到 Flutter 中

  ```dart
  const base = 'core';
  final path = Platform.isWindows ? '$base.dll' : 'lib$base.so';
  late final dylib = Platform.isIOS
      ? DynamicLibrary.process()
      : Platform.isMacOS
          ? DynamicLibrary.executable()
          : DynamicLibrary.open(path);
  api = ReporthCoreImpl(dylib);
  ```

  * 先引用so文件，这里还要考虑windows平台，所以也可能是dll。
  * 应用后直接使用就行了，接口是根据api.rs生成的，所有出入参都有Dart对应的类（名字一样，类型可能有细微差别）。
