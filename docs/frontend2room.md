[TOC]

# 网站前端与评测端房间系统交互文档



## 概要

​	该文档指定了网站前端与评测机上的房间系统交互的接口，双方采用`Websocket协议`进行通信。



## 定义

### 房间id

​	对于前端，房间`id`用于确定房间列表中的房间。



### 房间token

​	房间`token`是针对用户计算而来的，一个`token`应由如下规则生成：

```python
token = JUDGE_IP + ":" + JUDGE_PORT + "@" + ROOM_ID + "/" + USER_NAME
```

​	其中`JUDGE_IP`为评测机的IP地址，`JUDGE_PORT`为评测机侦听该房间信息用的端口，`ROOM_ID`为房间的`ID`，`USER_NAME`为该用户的用户名。



## 控制流程

* 评测机向前端发送房间列表
* 前端向评测机获取房间列表
* 用户网站前端申请创建房间
  * 评测机创建房间并分配房间号
* 用户在网站前端申请加入房间
  * 评测机与前端建立连接，并向房间广播
* 网站前端申请添加AI
  * 评测机启动AI，并广播AI准备状态
* 用户在播放器申请加入房间
  * 评测机连接播放器并向房间广播
* 用户在前端申请开始游戏
  * 评测机开始游戏，并向房间广播



## 通信

如无特例，通信均采用`json`格式的数据进行交互。



### 前端与评测机建立连接

​	连接成功后，评测机立刻推送房间列表。



### 评测机推送房间列表

​	评测机发送

```json
{
	'type': 'push_room_list',
    'room': room_list,
}
```

​	其中`room_list`为房间列表，其中的对象如下：

```json
{
    'id': room_id,
    'host': host_username,
    'players': username_list,
    'game': game_id,
    'private': Boolean
}
```

​	`room_id`为房间id，`host_username`为房主的用户名，`username_list`为该房间中的玩家的`username`列表，`game_id`为房间中的游戏的id，`private`项表示该房间是否拥有密码。	



### 评测机创建房间

​	创建完后评测机将会更新房间列表并作推送，同时发送如下信息

```json
{
    'type': 'room_created',
    'user': username,
    'room': room_id
}
```



### 评测机向房间内用户广播房间信息

​	当某个房间内的状态发生变化(增加AI/用户出入等)时，评测机向房间内的用户广播如下信息

```json
{
    'type': 'status',
    'users': user_list,
    'players': player_list,
    'game': game_id,
    'game_status': game_status, // bool
    'tokens': tokens
}
```

- `user_list`：用户名的列表

- `player_list`：玩家的列表，具体内容如下：

  - 

    ```json
    {
        'type': player_type, // 该用户是'ai'还是'human'还是'remote'
        'ready': is_ready, // 准备状态，Boolean
        'seat': seat, // 座次
        'name': username // 该玩家的用户名
    }
    ```

- `game_id`：该房间的游戏对应的id。

- `game_status`：该房间的对局是否已经开始。

- `tokens`：该房间的`token`列表，与`user_list`一一对应（即，每个玩家应有独立的房间`token`）。

  

### 前端申请加入房间

​	前端应与某个与`room_id`相关联的`websocket`建立连接。



### 前端向评测机获取房间列表

​	前端发送

```json
{
	'type': 'get_room_list'
}
```

​	评测机将回复一个房间列表的推送。



### 前端申请创建房间

​	前端发送

```json
{
    'type': 'create_room',
    'user': username,
    'game': game_id,
    'private': Boolean
}
```



### 前端请求更换座次

​	当前端请求更换座次时，应发送如下信息：

```json
{
    'type': 'change_seat',
    'before': seat_before,
    'after': seat_after
}
```

​	其中`seat_before`为更换前的座次，`seat_after`为更换后的座次。



### 前端申请准备本地AI

​	当某个用户添加本地AI时，前端应发送如下消息：

```json
{
    'type': 'ready_ai',
    'seat': seat,
    'ai': ai_token
}
```

​	其中`seat`为该AI的座次，应当为一整数， `ai_token`为某个AI的唯一标识符。	



### 前端取消准备本地AI

​	当某个用户删除本地AI时，前端应发送如下信息：

```json
{
    'type': 'cancel_ai',
    'seat': seat,
    'ai': ai_token
}
```

​	其中`seat`为该AI的座次，应当为一整数， `ai_token`为某个AI的唯一标识符。	



### 前端请求游戏开始

​	前端发送：

```json
{
	'type': 'start'
}
```

​	表示请求游戏开始。
