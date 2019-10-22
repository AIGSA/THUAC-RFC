## 评测端：Controller

评测端通过`Controller`负责与前后端交互、启动编译和启动游戏对局。

Controller启动：  
`python3 controller.py`

注意在启动controller时，需要保证有服务器在指定ip和端口上运行。  

### Controller接口
#### 与服务器接口
controller与服务器之间使用websocket连接，双方交互信息逻辑如下  
1. controller向服务器发送字符串“Ready”表示controller已经初始化完成
2. 服务器向controller发送字符串“Ready”表示服务器已初始化完成
3. controller每0.1s向服务器发送一个字符串“Request_For_Commands“，表示拉取请求
4. 若服务器没有接受到请求，则返回字符串”No_Commands“；若接收到请求，则根据请求类型返回字符串”Start_Game_Command“或”Compile_Command“
5. 若服务器返回“Start_Game_Command”，则服务器需要再发送一个json文件，文件格式为`{"Game_ID": xxx, "Team_Number": xxx, "Teams": ["Team_A": "{"Player_Number": "xxx", "Player_A": "AI_Token\Human_Player", ...}", ...]}`分别表示游戏ID、队伍数量、队伍信息等
6. 若服务器返回“Compile_Command”，则服务器需要再发送一个json文件，文件格式为`{"File_Name": filename, "File": file, "Token": token}`，分别表示文件名称、文件内容、文件标识符
7. 游戏结束时，controller向服务器发送信息[待定]
8. 编译结束时，controller向服务器发送消息[待定]
