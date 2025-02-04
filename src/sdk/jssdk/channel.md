# 频道管理

## 数据结构说明

频道资料的数据结构

```js

class ChannelInfo {
    /* tslint:disable-line */
    channel!: Channel; // 频道
    title!: string; // 频道标题
    logo!: string; // 频道logo
    mute!: boolean; // 是否免打扰
    top!: boolean; // 是否置顶
    orgData: any; //  第三方数据
    online: boolean = false // 是否在线
    lastOffline: number = 0 // 最后一次离线时间
}

```

订阅者的数据结构

```js

class Subscriber {
    /* tslint:disable-line */
    uid!: string; // 订阅者uid
    name!: string; // 订阅者名称
    remark!: string; // 订阅者备注
    avatar!: string; // 订阅者头像
    role!: number; // 订阅者角色
    channel!: Channel; // 频道
    version!: number; // 数据版本
    isDeleted!: boolean; // 是否已删除
    status!: number; // 订阅者状态
    orgData: any; //  第三方数据
}

```

## 频道资料

### 请求频道资料

`需要实现获取频道资料的数据源` [获取频道资料数据源](sdk/jssdk/datasource.html#获取频道资料数据源)

```js
// 强制从服务器获取频道资料并放入缓存
  WKSDK.shared().channelManager.fetchChannelInfo(channel)
```

### 获取频道资料

```js
// 从缓存获取频道资料
  const channelInfo =  WKSDK.shared().channelManager.getChannelInfo(channel)
```


### 监听频道资料

`  WKSDK.shared().channelManager.fetchChannelInfo(channel)方法会触发此监听`

```js

const listen = (channelInfo: ChannelInfo) => {
   
}

```

添加监听

```js   

WKSDK.shared().channelManager.addListener(listen)

```

移出监听

```js

WKSDK.shared().channelManager.removeListener(listen)

```


## 订阅者（成员）

### 同步频道的订阅者列表

`需要实现同步频道订阅者数据源` [同步频道订阅者数据源](sdk/jssdk/datasource.html#同步频道订阅者数据源)

```js
WKSDK.shared().channelManager.syncSubscribes(channel)
```


### 获取频道订阅者列表

```js

const subscribers = WKSDK.shared().channelManager.getSubscribes(channel)

```

### 获取我在频道内的订阅者身份

```js

const subscriber = WKSDK.shared().channelManager.getSubscribeOfMe(channel)

```

### 监听某个频道订阅者变化

```js

const listen = (channel: Channel) => {
   const subscribers = WKSDK.shared().channelManager.getSubscribes(channel)
}

```

添加监听

```js

WKSDK.shared().channelManager.addSubscriberChangeListener(listen)

```

移出监听

```js

WKSDK.shared().channelManager.removeSubscriberChangeListener(listen)

```


## 流消息（适合ChatGPT）

### 订阅频道流消息

待补充

### 取消订阅频道流消息

待补充
