# TVBoxOS 修改说明

项目代码来源俊佬：[TVBoxOS](https://github.com/q215613905/TVBoxOS)

## 本版本新增内容

在原项目基础上，新增了**弹幕搜索自定义扩展接口**。

App 端提供弹幕搜索按钮，点击和长按事件由开发者在 dex 侧自行实现具体行为。

目前传递的信息包括：

- 当前影片名称 `name`
- 当前集数名称 `episode`
- 点击类型：普通点击 / 长按

## 调用逻辑

播放页弹幕搜索按钮由 App 端控制：

1. 用户点击弹幕搜索按钮
2. App 获取当前影片名称和当前集数名称
3. App 异步调用外部 dex 中的弹幕搜索方法
4. dex 侧根据传入的 `name` 和 `episode` 自行处理搜索逻辑

普通点击会调用：

```java
com.github.catvod.spider.Danmaku.onClick(String name, String episode)
```

长按会调用：

```java
com.github.catvod.spider.Danmaku.onLongClick(String name, String episode)
```

如果外部 dex 没有实现对应方法，App 会自动忽略，不影响正常播放。

## 开发者如何对接

开发者只需要在自己的 dex 中添加 `com.github.catvod.spider.Danmaku` 类，并按需实现以下静态方法：

```java
package com.github.catvod.spider;

public class Danmaku {
	public static void onClick(String name, String episode) {
		// 普通点击弹幕搜索按钮时触发
		// name    当前影片名称
		// episode 当前集数名称
	}

	public static void onLongClick(String name, String episode) {
		// 长按弹幕搜索按钮时触发
		// name    当前影片名称
		// episode 当前集数名称
	}
}
```

说明：

- `onClick`：普通点击弹幕搜索按钮时调用。
- `onLongClick`：长按弹幕搜索按钮时调用。
- 两个方法都是可选的，可以只实现其中一个或不现实。
- 参数必须是两个 `String`，顺序为 `name, episode`。
- App 端会异步调用，不在主线程。

## 示例

```java
package com.github.catvod.spider;

import android.util.Log;

public class Danmaku {
	public static void onClick(String name, String episode) {
		Log.i("Danmaku", "点击搜索弹幕: " + name + " - " + episode);
		// 在这里实现自己的弹幕搜索逻辑
	}

	public static void onLongClick(String name, String episode) {
		Log.i("Danmaku", "长按搜索弹幕: " + name + " - " + episode);
		// 在这里实现自己的长按逻辑，例如打开自定义搜索入口
	}
}
```
