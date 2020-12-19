## 单个变量缓存

优点： 方便、易理解

缺点： 每个缓存函数都需要额外维护一个`*Cache`变量
```dart
String _getNativeVersionCache;
String getNativeVersion() {
	_getNativeVersionCache ??= Native.getVersion();
	return _getNativeVersionCache;
}
```
## 缓存辅助函数

优点： 常用、只需暴露一个`memorize`函数

缺点： `memorize`通常需要多次重载（处理不同参数的函数）
```dart
T Function() memorize<T>(T Function() fun) {
	T cache;
	return () {
		cache ??= fun();
		return cache;
	};
}

String _getNativeVersion() {
	return Native.getVersion();
}

String getNativeVersion = memorize(_getNativeVersion);
```

## 缓存辅助对象

优点： 方便、易理解、只需暴露一个`memorize`对象

缺点： 不常用
```dart
Map<String, dynamic> memorize = {};
String getNativeVersion() {
	memorize['getNativeVersion'] ??= Native.getVersion();
	return memorize['getNativeVersion'];
}
```

## 总结

|       |  单个变量缓存   | 缓存辅助函数  | 缓存辅助对象   |
|  ----  | ----           | ----         | ----          |
| 方便   |  √   | x  | √   |
| 易理解 |  √   | x  | √   |
| 易维护 |  x   | √  | √   |
| 常用   |  √   | √  | x   |
