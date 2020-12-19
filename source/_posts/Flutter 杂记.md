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