---
title: Git使用笔记
tags: [flutter, css]
---

## 背景
Flutter 有很多让我使用不适的地方，大部分不适感来源于其样式的配置方式，正好近期工作中需要使用动画，而我更希望能像写CSS动画一样轻松地地实现动画配置。

## Flutter 动画原理分析
我们可以先思考下，如果 Flutter 没有提供动画支持，我们如何让 Element 动起来？应该可以想到，大概需要两个条件:
1. 动态去修改 Element 的样式属性。
2. Element 映射的 Widget 需要是 `StatefulWidget` ，因为我们需要更新 Element 的配置信息。 

那么 Flutter 提供的 Animation 体系是这样做的吗？借由下方简单示例，我们简单跟一下它的实现逻辑。

```dart
import 'dart:math' as math;

class RotateWrapper extends StatefulWidget {
  _RotateWrapperState createState() => _RotateWrapperState();
}

class _RotateWrapperState extends State<RotateWrapper>
    with TickerProviderStateMixin {
  Animation<double> animation;
  AnimationController controller;

  void starAnimation() {
    controller =
        AnimationController(duration: const Duration(seconds: 1), vsync: this);
    animation = Tween<double>(begin: 0, end: 2 * math.pi).animate(controller)
      ..addListener(() {
        setState(() {});
      });    
    controller.forward();
  }

  @override
  void initState() {
    super.initState();
  }

  @override
  Widget build(BuildContext context) {
    return Column(
      children: <Widget>[
        Center(
          child: Transform.rotate(
            angle: animation?.value ?? 0,
            origin: Offset.zero,
            child: Container(
                height: 50,
                width: 50,
                color: Colors.red,
                child: Text('G'),
                ),
          ),
        ),
        OutlineButton(
          onPressed: starAnimation,
          child: Text('Start Animation'),
        ),
      ],
    );
  }

  @override
  void dispose() {
    controller.dispose();
    super.dispose();
  }
}
```

### a: 初始化控制器
0. 解读入口`controller = AnimationController(duration: const Duration(seconds: 1), vsync: this);`
1. `AnimationController`初始化时主要执行两个逻辑: `createTicker`和`_internalSetValue`，其中`createTicker`带有入参`_tick`
	```dart
	  AnimationController({
	    double value,
	    this.duration,
	    this.reverseDuration,
	    this.debugLabel,
	    this.lowerBound = 0.0,
	    this.upperBound = 1.0,
	    this.animationBehavior = AnimationBehavior.normal,
	    @required TickerProvider vsync,
	  }) : assert(lowerBound != null),
	       assert(upperBound != null),
	       assert(upperBound >= lowerBound),
	       assert(vsync != null),
	       _direction = _AnimationDirection.forward {
	    _ticker = vsync.createTicker(_tick);
	    _internalSetValue(value ?? lowerBound);
	  }
	```
2. `vsync.createTicker`其中`vsync`是从`TickerProviderStateMixin`提供的，这个混入实现了`TickerProvider`，它的主要职责是完成对`_WidgetTicker`的实例化，并收集实例

	```dart
	  /// class [TickerProviderStateMixin]
	  Ticker createTicker(TickerCallback onTick) {
	    _tickers ??= <_WidgetTicker>{};
	    final _WidgetTicker result = _WidgetTicker(onTick, this, debugLabel: 'created by $this');
	    _tickers.add(result);
	    return result;
	  }
	```
3. `_WidgetTicker`继承了`Ticker`并把`AnimationController`提供的入参`_tick`传给了`Ticker`的构造函数
	```dart
	  /// class [_WidgetTicker]
	  _WidgetTicker(TickerCallback onTick, this._creator, { String debugLabel }) : super(onTick, debugLabel: debugLabel);
	```
4. `Ticker`收集了入参`_tick`，把它记录在了`_onTick`这个属性中，我们暂且不管它会在何时被调用
	```dart
	  /// class [Ticker]
	  Ticker(this._onTick, { this.debugLabel }) {
	    assert(() {
	      _debugCreationStack = StackTrace.current;
	      return true;
	    }());
	  }
	```
5. `_internalSetValue`的职责是修改 controller 的状态
	```dart
	  /// class [AnimationController] 
	  void _internalSetValue(double newValue) {
	    _value = newValue.clamp(lowerBound, upperBound) as double;
	    if (_value == lowerBound) {
	      _status = AnimationStatus.dismissed;
	    } else if (_value == upperBound) {
	      _status = AnimationStatus.completed;
	    } else {
	      _status = (_direction == _AnimationDirection.forward) ?
	        AnimationStatus.forward :
	        AnimationStatus.reverse;
	    }
	  }
	```

### b: 开始动画
0. 解读入口`controller.forward();`
1. `forward`中设定运动方向为`_AnimationDirection.forward`，然后调用了`_animateToInternal`
```dart
  TickerFuture forward({ double from }) {
  	// 相比于源码删除了断言
    _direction = _AnimationDirection.forward;
    if (from != null)
      value = from;
    return _animateToInternal(upperBound);
  }
```
2. `_animateToInternal`


## 差异对比
这里以最简单的单关键帧周期放大动画举例，CSS 是这样子配置的：
```css
.animate-item-scale {
  animation-duration: 1s;
  animation-timing-function: ease-in-out;
  animation-name: animateScale;
}

@keyframes animateScale {
  0% {
    transform: scale(1);
  }
  100% {
    transform: scale(2);
  }
}
```

Flutter 动画则如下：


