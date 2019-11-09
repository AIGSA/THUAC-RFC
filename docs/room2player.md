# 播放器与房间连接的通信

​	播放器与评测端的房间通过`websocket`进行连接，以`json`格式的数据进行通信。



## 播放器对房间的通信



### 连接房间

​	一个房间的`token`按如下规则生成：

```python
token = JUDGE_IP + ":" + JUDGE_PORT + "/" + ROOM_ID + "/" + USER_NAME
```

​	其中`JUDGE_IP`为评测机的地址，`JUDGE_PORT`为评测机通信端口，`ROOM_ID`为房间编号，`USER_NAME`为播放器用户的用户名。

​	播放器需要一个`token`来解析评测机的IP地址与端口，并与房间进行连接。

​	在播放器中填入`token`并与房间连接之后，需要发送如下信息：

```json
{
    "token": token,
    "request": "connect"
}
```





### 发送回合数据

​	播放器在用户进行操作后需要发送回合数据给房间，格式如下：

```json
{
    "token": token,
    "request": "action",
    "content": content
}
```

​	其中`content`为播放器需要发送给游戏逻辑的回合信息，它应当是一个字符串，房间会将`content`原封不动地转发给游戏逻辑。`content`的内容应与游戏逻辑进行约定。



## 房间对播放器的通信



### 连接房间

​	当房间与播放器连接成功时，房间会回传连接成功的消息，如下：

```json
{
    "request": "connected"
}
```



## 回合信息

​	当游戏逻辑出现了新的回合信息（或游戏结束信息）、需要发送给播放器时，房间会发送如下信息：

```json
{
    "request": "action",
    "content": content
}
```

​	其中`content`为游戏逻辑发送给播放器的回合信息，它是一个字符串。`content`的内容应与游戏逻辑进行约定。