## tapable

Tapable 是一个类似于 Node.js 的 EventEmitter 的库, 主要是控制钩子函数的发布与订阅,控制着 webpack 的插件系统。 

| hook                     | function                                                     |
| ------------------------ | ------------------------------------------------------------ |
| SyncHook                 | 同步，基本hook，仅调用绑定的function                         |
| SyncBailHook             | 同步熔断hook。当任意绑定的function有返回值时，则提前退出     |
| SyncWaterfallHook        | 同步瀑布hook。将返回值传递给下一个function                   |
| SyncLoopHook             | 同步循环hook。监听函数返回true表示继续循环，返回undefined则结束循环 |
| AsyncParallelHook        | 异步并行hook                                                 |
| AsyncParallelBailHook    | 异步并行熔断hook                                             |
| AsyncSeriesHook          | 异步串行hook                                                 |
| AsyncSeriesBailHook      | 异步串行熔断hook                                             |
| AsyncSeriesWaterfallHook | 异步串行瀑布hook                                             |

hook的绑定和执行

| Async*                        | Sync*      |
| ----------------------------- | ---------- |
| 绑定：tabAsync/tabPromise/tap | 绑定：tap  |
| 执行：callAsync/promise       | 执行：call |

```javascript
class Car {
	constructor() {
		this.hooks = {
			accelerate: new SyncHook(["newSpeed"]),
			brake: new SyncHook(),
			calculateRoutes: new AsyncParallelHook(["source", "target", "routesList"])
		};
	}

	/* ... */
}
```

```javascript
// 绑定
const myCar = new Car();
myCar.hooks.brake.tap("WarningLampPlugin", () => warningLamp.on());
myCar.hooks.accelerate.tap("LoggerPlugin", newSpeed => console.log(`Accelerating to ${newSpeed}`));

myCar.hooks.calculateRoutes.tapPromise("GoogleMapsPlugin", (source, target, routesList) => {
	// return a promise
	return google.maps.findRoute(source, target).then(route => {
		routesList.add(route);
	});
});
myCar.hooks.calculateRoutes.tapAsync("BingMapsPlugin", (source, target, routesList, callback) => {
	bing.findRoute(source, target, (err, route) => {
		if(err) return callback(err);
		routesList.add(route);
		// call the callback
		callback();
	});
});

// You can still use sync plugins
myCar.hooks.calculateRoutes.tap("CachedRoutesPlugin", (source, target, routesList) => {
	const cachedRoute = cache.get(source, target);
	if(cachedRoute)
		routesList.add(cachedRoute);
})
```

```javascript
// 执行
class Car {
	/**
	  * You won't get returned value from SyncHook or AsyncParallelHook,
	  * to do that, use SyncWaterfallHook and AsyncSeriesWaterfallHook respectively
	 **/

	setSpeed(newSpeed) {
		// following call returns undefined even when you returned values
		this.hooks.accelerate.call(newSpeed);
	}

	useNavigationSystemPromise(source, target) {
		const routesList = new List();
		return this.hooks.calculateRoutes.promise(source, target, routesList).then((res) => {
			// res is undefined for AsyncParallelHook
			return routesList.getRoutes();
		});
	}

	useNavigationSystemAsync(source, target, callback) {
		const routesList = new List();
		this.hooks.calculateRoutes.callAsync(source, target, routesList, err => {
			if(err) return callback(err);
			callback(null, routesList.getRoutes());
		});
	}
}
```

