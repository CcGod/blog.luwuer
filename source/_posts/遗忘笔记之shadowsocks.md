---
title: 遗忘笔记之shadowsocks
tags: [遗忘笔记, linux, shadowsocks]
date: 2017-09-27 17:40:50
---

### shadowsocks重启
1. `ps -ef|grep shadowsocks` 查看进程和配置文件路径
2. `kill -quit 823 ` 杀死已存在进程
3. `ss-server -c /etc/shadowsocks-libev/config.json start`启动进程（restart重启）

> shadowsocks可执行文件在`/usr/local/bin/ss-server`，如果报错`ss-server: command not found`就去这里执行第三部的命令；另很多教程在start前面加上一个-d会导致nameservers报错`failed to set nameservers`，因为加上-d shadowsocks会默认去`/etc/resolv.conf`里边取值，而不是你shadowsocks的配置文件。

### 检查当前页面执行环境中有哪些全局变量是开发者添加的

1. 在当前页面添加空白 url 的 iframe
2. 比较当前页面环境 window 和 iframe 的  window 上全局变量挂载情况

```javascript
(function () {
  let iframe = document.createElement('iframe')
  let globalVars = Object.keys(window)
  let targetVars = []
  document.body.append(iframe)
  iframe.src = 'about:blank'
  iframe.onload = function() {
    const iframeGlobalVars = Object.keys(iframe.contentWindow)
    globalVars.forEach(key => {
      
      !iframeGlobalVars.includes(key) && console.log(key)
    })
  }
})()
```

### flutter 渲染

1. hooks.dart:                 _drawFrame -> _invoke
2. zone.dart:                  _CustomZone.runGuarded -> _CustomZone.run -> _rootRun
3. scheduler/bingding.dart:    _handleDrawFrame -> handleDrawFrame -> _invokeFrameCallBack -> 
4. rendering/bingding.dart:    _handlePersistentFrameCallback
5. widgets/bingding.dart:      drawFrame
6. rendering/bingding.dart:    drawFrame
7. rendering/object.dart:      flushLayout
8. rendering/object.dart:      _layoutWithoutResize
9. rendering/stack.dart:       performLayout
10. rendering/object.dart:     layout
11. rendering/proxy_box.dart   RenderOffstage.performLayout
12. rendering/object.dart:     layout -> 递归(10 - 11)但是是不同的 proxyObject.performLayout ，他们都最终继承于 RenderObject
13. ...
14. rendering/platform_view.dart   perfomResize(if this renderObject size by parent) -> _sizePlatformView
15. services/platform_views.dart  setSize -> _create
16. services/platform_channel.dart inkokeMethod

### flutter overflow 渲染规则

1. 如果没有 overflow 则正常渲染
2. 如果 overflow 并且当前容器宽高至少有一个为 0，则直接返回不渲染
3. 如果 overflow 并且当前容器宽高不为 0，则裁剪 overflow 的部分

`https://github.com/flutter/flutter/blob/master/packages/flutter/lib/src/rendering/flex.dart`
```dart
  void paint(PaintingContext context, Offset offset) {
  	// @1
    if (!_hasOverflow) {
      defaultPaint(context, offset);
      return;
    }

    // @2
    // There's no point in drawing the children if we're empty.
    if (size.isEmpty)
      return;

    // @3
    // We have overflow. Clip it.
    context.pushClipRect(needsCompositing, offset, Offset.zero & size, defaultPaint);
    // ...
```

### Flutter 获取图片 raga 像素数据

```dart
import 'dart:ui' as ui;
import 'package:flutter/material.dart';

// 以本地图片为例：
List RGBAList;
// 1. 获取 ImageProvider: 通过 `ExactAssetImage('$local_img_uri')` 获取 ExactAssetImage 对象
// ExactAssetImage 继承 AssetBundleImageProvider 继承 ImageProvider
ExactAssetImage provider = ExactAssetImage('$local_img_uri');

// 2. 通过 ImageProvider 获取 ui.Image 对象
ImageStream stream = provider.resolve(ImageConfiguration.empty);
Completer completer = Completer<ui.Image>();
ImageStreamListener listener = ImageStreamListener((frame, sync) {
	ui.Image image = frame.image;
	completer.complete(image);
	stream.removeListener(listener);
})
stream.addListener(listener);

// 3. 通过 ui.Image 对象获得 rgba 像素数据
completer.then((ui.Image image) {
	image.toByteData(format: ui.ImageByteFormat.rowRgba).then((ByteData data) {
		RGBAList = data.buffer.asUint8List().toList();
	});	
})
```

### Android: 查看应用线程
```bash
# 1. 多个 adb 连接查看设备信息
adb devices
# 结果：
# 17f4f7f6        device
# e1ed6a68        device

# 2. 连接目标设备
adb -s e1ed6a68 shell

# 3. 查看应用进程
ps -ef|grep flutter_app
# 结果：
# u0_a576      10323   720 90 14:27:12 ?    00:00:04 com.example.flutter_app
# root         10379 10284 5 14:27:16 pts/0 00:00:00 grep flutter_app

# 4. 查看进程的线程
# 也可以使用
ps -T -p 10323
# 结果：
# USER           PID   TID  PPID     VSZ    RSS WCHAN            ADDR     FRZ  S CMD
# u0_a576      10323 10323   720 4500244 247092 SyS_epoll+          0     efg  S ple.flutter_app
# u0_a576      10323 10328   720 4500244 247092 futex_wai+          0     efg  S Jit thread pool
# u0_a576      10323 10329   720 4500244 247092 do_sigtim+          0     efg  S Signal Catcher
# u0_a576      10323 10330   720 4500244 247092 poll_sche+          0     efg  S ADB-JDWP Connec
# u0_a576      10323 10331   720 4500244 247092 futex_wai+          0     efg  S ReferenceQueueD
# u0_a576      10323 10332   720 4500244 247092 futex_wai+          0     efg  S FinalizerDaemon
# u0_a576      10323 10333   720 4500244 247092 futex_wai+          0     efg  S FinalizerWatchd
# u0_a576      10323 10334   720 4500244 247092 futex_wai+          0     efg  S HeapTaskDaemon
# u0_a576      10323 10335   720 4500244 247092 binder_th+          0     efg  S Binder:10323_1
# u0_a576      10323 10336   720 4500244 247092 binder_th+          0     efg  S Binder:10323_2
# u0_a576      10323 10337   720 4500244 247092 binder_th+          0     efg  S Binder:10323_3
# u0_a576      10323 10338   720 4500244 247092 futex_wai+          0     efg  S Profile Saver
# u0_a576      10323 10339   720 4500244 247092 futex_wai+          0     efg  S AsyncTask #1
# u0_a576      10323 10342   720 4500244 247092 SyS_epoll+          0     efg  S 1.ui
# u0_a576      10323 10343   720 4500244 247092 SyS_epoll+          0     efg  S 1.gpu
# u0_a576      10323 10344   720 4500244 247092 SyS_epoll+          0     efg  S 1.io
# u0_a576      10323 10345   720 4500244 247092 futex_wai+          0     efg  S ple.flutter_app
# u0_a576      10323 10346   720 4500244 247092 futex_wai+          0     efg  S ple.flutter_app
# u0_a576      10323 10347   720 4500244 247092 futex_wai+          0     efg  S ple.flutter_app
# u0_a576      10323 10348   720 4500244 247092 futex_wai+          0     efg  S ple.flutter_app
# u0_a576      10323 10349   720 4500244 247092 futex_wai+          0     efg  S ple.flutter_app
# u0_a576      10323 10350   720 4500244 247092 futex_wai+          0     efg  S ple.flutter_app
# u0_a576      10323 10351   720 4500244 247092 futex_wai+          0     efg  S ple.flutter_app
# u0_a576      10323 10352   720 4500244 247092 futex_wai+          0     efg  S ple.flutter_app
# u0_a576      10323 10353   720 4500244 247092 SyS_epoll+          0     efg  S ple.flutter_app
# u0_a576      10323 10356   720 4500244 247092 futex_wai+          0     efg  S ple.flutter_app
# u0_a576      10323 10357   720 4500244 247092 SyS_epoll+          0     efg  S JsThread
# u0_a576      10323 10358   720 4500244 247092 futex_wai+          0     efg  S V8 DefaultWorke
# u0_a576      10323 10359   720 4500244 247092 futex_wai+          0     efg  S V8 DefaultWorke
# u0_a576      10323 10360   720 4500244 247092 futex_wai+          0     efg  S V8 DefaultWorke
# u0_a576      10323 10361   720 4500244 247092 futex_wai+          0     efg  S V8 DefaultWorke
# u0_a576      10323 10362   720 4500244 247092 futex_wai+          0     efg  S V8 DefaultWorke
# u0_a576      10323 10363   720 4500244 247092 futex_wai+          0     efg  S V8 DefaultWorke
# u0_a576      10323 10364   720 4500244 247092 futex_wai+          0     efg  S V8 DefaultWorke
# u0_a576      10323 10365   720 4500244 247092 SyS_epoll+          0     efg  S RenderThread
# u0_a576      10323 10366   720 4500244 247092 SyS_epoll+          0     efg  S queued-work-loo
# u0_a576      10323 10367   720 4500244 247092 futex_wai+          0     efg  S Timer-0
# u0_a576      10323 10368   720 4500244 247092 futex_wai+          0     efg  S 1.ui
# u0_a576      10323 10369   720 4500244 247092 futex_wai+          0     efg  S Thread-3
# u0_a576      10323 10371   720 4500244 247092 futex_wai+          0     efg  S hwuiTask1
```

### Android: 查看应用内存
```bash
# 1. 多个 adb 连接查看设备信息
adb devices
# 结果：
# 17f4f7f6        device
# e1ed6a68        device

# 2. 连接目标设备
adb -s e1ed6a68 shell

# 3. 查看具体应用占用内存
dumpsys meminfo [在 AndroidManifest.xml 中配置的具体包名]
```

### 杂项
1. Android push 包到指定设备: `adb -s e1ed6a68 push <local> <remote>` (`remote = '/sdcard/rpktest/'`)

