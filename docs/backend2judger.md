# 网站后端与测评端交互文档

## 概要

该文档定义了网站后端与测评端交互的接口。原则上双方通信均采用 Websocket 协议，除传输文件外，信息一律采用 JSON 编码。

## 定义

网站后端将 Websocket Server 启动在 `ws://HOSTNAME:PORT/ws` 上

## 基本流程

1. 网站后端启动 Websocket Server
2. 测评端通过 Websocket 连接至网站后端
3. 测评端发送初始化指令，此时网站后端认为测评端上线
4. 测评端向网站后端以论询的方式发送请求
5. 测评端断开 Websocket，此时网站后端认为测评端下线

## API

### 初始化

#### 测评端发送

```json
{
    type: 0,
    data: {
 		description,
    	address
	}
}
```

**data.description : string : required**

描述测评端的信息，用于前端显示

**data.address : string : optional**

测评端的地址，形如 `1.1.1.1:5000`，必须外网可访问。

如果提供地址，该测评端将被认为提供了一个完整的房间系统服务，会在网站前端的房间服务器列表中进行显示。

如果未提供地址，则网站认为测评端不提供房间系统服务。

#### 网站后端发送

```json
{
    "type": 0
    "data": {
    	success,
    	msg
	}
}
```

**data.success : boolean : required**

网站后端是否成功接受测评机的初始化请求

**data.msg : string : required**

网站后端返回的信息


### 任务请求

#### 测评端发送

```json
{
    "type": 1
}
```

表示测评端希望向网站后端拉取一个测评任务

### 对局测评任务

#### 网站后端发送

```json
{
    "type": 2
    "data": {
		game_token,
    	team_number,
    	"teams": [
    		{
				player_number,
    			"players": [
    				{
						type,
    					token
					}
    			],
			}
	}
}
```

#### 评测端发送

```json
{
    "type": 2
    "data": {
		game_token,
		result
	}
}
```

### AI 编译任务

#### 网站后端发送

```json
{
    "type": 3
    "data": {
		ai_token
	}
}
```

**ai_token : string : required **

表示待编译的 AI 的 token

#### 评测端发送

```json
{
    "type": 3
    "data": {
		ai_token,
		result
	}
}
```

### 游戏包下载请求

#### 测评端发送

```json
{
    "type": 3
    "data": {
		game_token
	}
}
```

**game_token : string : required **

表示需要下载的游戏包的 token

#### 网站后端发送

```json
{
    "type": 3,
    game_id,
    data_length,
    data
}
```

传输二进制数据

1. 前两个 Byte 表示 `type`，为 short 类型
2. 接下来 8 个 Byte 表示 `game_token`，为 `long long` 类型
3. 接下来 8 个 Byte 表示数据长度 `data_length`
4. 后面 `data_length` 个字节表示传输的数据

### AI 包下载请求

#### 测评端发送

```json
{
    "type": 3
    "data": {
		ai_token
	}
}
```

**ai_token : string : required **

表示需要下载的 AI 的 token

#### 网站后端发送

```json
{
    "type": 3,
    ai_id,
    data_length,
    data
}
```

传输二进制数据

1. 前两个 Byte 表示 `type`，为 short 类型
2. 接下来 8 个 Byte 表示 `ai_token`，为 `long long` 类型
3. 接下来 8 个 Byte 表示数据长度 `data_length`
4. 后面 `data_length` 个字节表示传输的数据

