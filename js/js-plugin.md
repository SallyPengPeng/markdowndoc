## classnames

```javascript
import classNames from 'classnames';

classNames('foo', 'bar'); // => 'foo bar'
classNames('foo', { bar: true }); // => 'foo bar'
classNames({ 'foo-bar': true }); // => 'foo-bar'
classNames({ 'foo-bar': false }); // => ''
classNames({ foo: true }, { bar: true }); // => 'foo bar'
classNames({ foo: true, bar: true }); // => 'foo bar'

// lots of arguments of various types
classNames('foo', { bar: true, duck: false }, 'baz', { quux: true }); // => 'foo bar baz quux'

// other falsy values are just ignored
classNames(null, false, 'bar', undefined, 0, 1, { baz: null }, ''); // => 'bar 1'
```

## moment

```javascript
moment(); // 获取当前的日期和时间的moment对象

moment("1995-12-25"); // 获取指定日期的moment对象
moment("12-25-1995", "MM-DD-YYYY");
moment("12-25-1995", ["MM-DD-YYYY", "YYYY-MM-DD"]);

moment({ y    :2010, M     :3, d   :5, h    :15, m      :10, s      :3, ms          :123});
moment({ year :2010, month :3, day :5, hour :15, minute :10, second :3, millisecond :123});
moment(1318781876406);
moment(new Date(2011, 9, 16));

// 获取或设置
moment().day(-7); // 上个星期日 (0 - 7)
moment().day(0); // 这个星期日 (0)
moment().day(7); // 下个星期日 (0 + 7)
moment().get('year');
moment().get('month');  // 0 至 11
moment().set('year', 2013);
moment().set('month', 3);  // 四月

moment.min(Moment[,Moment...]);
moment.max(Moment[,Moment...]);

moment().add(7, 'days');

moment().format(); // "2014-09-08T08:02:17-05:00" (ISO 8601，无小数秒钟)
moment().format("dddd, MMMM Do YYYY, h:mm:ss a");
```

## rxjs

便捷的在组件中提供可观察的对象

```javascript
import { Observable } from 'rxjs';
 
const observable = new Observable(subscriber => {
  subscriber.next(1);
  subscriber.next(2);
  subscriber.next(3);
  setTimeout(() => {
    subscriber.next(4);
    subscriber.complete();
  }, 1000);
});
 
console.log('just before subscribe');
observable.subscribe({
  next(x) { console.log('got value ' + x); },
  error(err) { console.error('something wrong occurred: ' + err); },
  complete() { console.log('done'); }
});
console.log('just after subscribe');
```

```
// Which executes as such on the console:
just before subscribe
got value 1
got value 2
got value 3
just after subscribe
got value 4
done
```

## websocket

https://www.infoq.cn/article/ujwip4vyf3xjkphimtjc

websocket和http的区别：

1、WebSocket是双向通信协议，模拟Socket协议，可以双向发送或接受信息，而HTTP是单向的；

2、WebSocket是需要浏览器和服务器握手进行建立连接的，而http是浏览器发起向服务器的连接

默认情况下：

- 1）WebSocket 协议使用 80 端口；
- 2）若运行在 TLS 之上时，默认使用 443 端口。

客户端：

```javascript
function connectWebsocket() {
    ws = new WebSocket('ws://localhost:9000');
    // 监听连接成功
    ws.onopen = () => {
        console.log('连接服务端WebSocket成功');
        ws.send(JSON.stringify(msgData));	// send 方法给服务端发送消息
    };

    // 监听服务端消息(接收消息)
    ws.onmessage = (msg) => {
        let message = JSON.parse(msg.data);
        console.log('收到的消息：', message)
        elUl.innerHTML += `<li class="b">小秋：${message.content}</li>`;
    };

    // 监听连接失败
    ws.onerror = () => {
        console.log('连接失败，正在重连...');
        connectWebsocket();
    };

    // 监听连接关闭
    ws.onclose = () => {
    	console.log('连接关闭');
    };
};
connectWebsocket();
```

从上面可以看到 WebSocket 实例的 API 很容易理解，简单好用，通过 send() 方法可以发送消息，onmessage 事件用来接收消息，然后对消息进行处理显示在页面上。当 onerror 事件（监听连接失败）触发时，最好进行执行重连，以保持连接不中断。

服务端 Node: （这里使用 ws 库）

```javascript
const path = require('path');
const express = require('express');
const app = express();
const server = require('http').Server(app);
const WebSocket = require('ws');

const wss = new WebSocket.Server({ server: server });

wss.on('connection', (ws) => {

  // 监听客户端发来的消息
  ws.on('message', (message) => {
    console.log(wss.clients.size);
    let msgData = JSON.parse(message);
    if (msgData.type === 'open') {
      // 初始连接时标识会话
      ws.sessionId = `${msgData.fromUserId}-${msgData.toUserId}`;
    } else {
      let sessionId = `${msgData.toUserId}-${msgData.fromUserId}`;
      wss.clients.forEach(client => {
        if (client.sessionId === sessionId) {
          client.send(message);	 // 给对应的客户端连接发送消息
        }
      })
    }
  })

  // 连接关闭
  ws.on('close', () => {
    console.log('连接关闭');
  });
});
```

这样浏览器和服务端就可以愉快的发送消息了，效果如下：绿色箭头表示发出的消息，红色箭头表示收到的消息。

![image-20220627155220857](https://images-sally.oss-cn-beijing.aliyuncs.com/img/websocket-messages.png)
