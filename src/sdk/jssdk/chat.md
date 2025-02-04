# 聊天管理

## 消息发送


### 说明

发送的方法说明
```js


 /**
  *  发送消息
  * @param content  消息内容
  * @param channel 频道对象 个人频道，群频道
  * @returns 完整消息对象
*/
WKSDK.shared().chatManager.send(content: MessageContent, channel: Channel)


```

举例说明

``` js

// 例如发送文本消息hello给用户u10001
const text = new MessageText("hello") // 文本消息
WKSDK.shared().chatManager.send(text,new Channel("u10001",ChannelTypePerson))

// 例如发送文本消息hello给群频道g10001
WKSDK.shared().chatManager.send(text,new Channel("g10001",ChannelTypeGroup))

```

### 发送文本消息

``` js
// 文本消息
const msgContent = new MessageText("hello") 

// 发送
WKSDK.shared().chatManager.send(msgContent,channel)

```

### 发送图片消息

自己上传图片

``` js

// 图片消息
const msgContent = new MessageImage() 
msgContent.url = url // 图片的下载地址
msgContent.width = width // 图片宽度
msgContent.height = height // 图片高度

// 发送
WKSDK.shared().chatManager.send(msgContent,channel)

```

sdk去上传文件

`需要实现上传文件的数据源` 参考：[上传文件数据源](/sdk/jssdk/datasource.html#文件上传数据源)

``` js

const msgContent = new MessageImage() // 文本消息
msgContent.file = file  // 上传的图片文件对象, 例如 <input type="file" /> 获取到的文件对象 
msgContent.width = width // 图片宽度
msgContent.height = height // 图片高度

// 发送，sdk会判断图片是否已经上传过，如果没有上传过，会调用上传任务，上传图片
WKSDK.shared().chatManager.send(msgContent,channel)

```

### 发送自定义消息

参考自定义消息: [自定义消息](/sdk/jssdk/advance.html#自定义消息)

```ts

const msgContent = new XXXX() // XXXX为自定义消息的正文

// 发送
WKSDK.shared().chatManager.send(msgContent,channel)
```

## 消息监听

### 监听发送消息状态

``` js

const listen =   (packet: SendackPacket) => {
  console.log('消息clientSeq->', packet.clientSeq); // 消息客户端序号用来匹配对应的发送的消息
  if (packet.reasonCode === 1) {
    // 发送成功
  } else {
    // 发送失败
  }
}

```

添加监听

```js

// 消息发送状态监听
WKSDK.shared().chatManager.addMessageStatusListener(listen);

```


移出监听

```js
// 消息发送状态监听
WKSDK.shared().chatManager.removeMessageStatusListener(listen)

```

### 监听常规消息


``` js

const listen =   (message: Message) => {
    message.content // 消息内容
    message.channel // 消息频道
    message.fromUID // 消息发送者
    ....
}

```

添加监听

```js
WKSDK.shared().chatManager.addMessageListener(listen);
```


移出监听

```js
WKSDK.shared().chatManager.removeMessageListener(listen)
```

### 监听cmd消息

``` js

const listen =   (message: Message) => {
    const cmdContent = message.content as CMDContent
    const cmd = cmdContent.cmd   // 指令名称
    const param = cmdContent.param // 指令参数
    ....
}

```


添加监听

```js

WKSDK.shared().chatManager.addCMDListener(listen)

```


移出监听

```js
WKSDK.shared().chatManager.removeCMDListener(listen)

```

## 历史消息

`需要实现同步频道消息数据源` 参考：[同步频道消息数据源](/sdk/jssdk/datasource.html#同步频道消息数据源)

获取某个频道的历史消息

```js

const messages = await WKSDK.shared().chatManager.syncMessages(channel, opts)


```

opts 参数解释

```js

{
    startMessageSeq: number = 0 // 开始消息列号（结果包含startMessageSeq的消息）
    endMessageSeq: number = 0 //  结束消息列号（结果不包含endMessageSeq的消息）0表示不限制
    limit: number = 30 // 每次限制数量
    pullMode: PullMode = PullMode.Down // 拉取模式 0:向下拉取 1:向上拉取
}

```

详细解释：

```
以startMessageSeq为基准 pullMode控制拉取方向，endMessageSeq和limit控制结束位置

------------------ 上拉 ------------------

pullMode为1 表示向上拉，逻辑如下：

消息以startMessageSeq为起点，加载大于或等于startMessageSeq的消息，加载到超过endMessageSeq（结果不包含endMessageSeq）或超过limit为止，如果endMessageSeq为0则以limit为准

例如：
startMessageSeq=100 endMessageSeq=200 limit=10 以limit为准，则返回的messageSeq为100-110的消息.
startMessageSeq=100 endMessageSeq=105 limit=10 以endMessageSeq为准，则返回的messageSeq为100-104的消息
startMessageSeq=100 endMessageSeq=0 limit=10 以limit为准，则返回的messageSeq为100-110的消息

------------------ 下拉 ------------------

pullMode为0 表示向下拉，逻辑如下：

消息以startMessageSeq为起点，加载小于或等于startMessageSeq的消息，加载到超过endMessageSeq（结果不包含endMessageSeq）或超过limit为止，如果endMessageSeq为0则以limit为准

例如：
startMessageSeq=100 endMessageSeq=50 limit=10 以limit为准，则返回的messageSeq为100-91的消息.
startMessageSeq=100 endMessageSeq=95 limit=10 以endMessageSeq为准，则返回的messageSeq为100-96的消息
startMessageSeq=100 endMessageSeq=0 limit=10 以limit为准，则返回的messageSeq为100-91的消息


如果startMessageSeq和endMessageSeq都为0，则不管pullMode为那种都加载最新的limit条消息。
```


## 离线消息

在悟空 **IM**中为了应付海量离线消息，采用了按需拉取的机制，比如 10 个会话一个会话 10 万条消息，**悟空 IM** 不会把100 万条消息都拉取到本地。 而是采用拉取这 10 个会话的信息和对应的最新 20 条消息，也就是实际只拉取了 200 条消息 相对 100 万条消息来说大大提高了离线拉取速度。用户点进对应的会话才会去按需拉取这个会话的消息。 这些机制 SDK 内部都已做好了封装，使用者其实不需要关心。使用者只需要关心最近会话的变化




