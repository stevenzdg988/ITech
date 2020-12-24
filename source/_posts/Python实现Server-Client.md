---
title: Python实现Server-Client
date: 2020-12-20 20:58:51
tags:
    - Python
    - import
    - Function
categories:
    - Python
---

为了尽快的把上一篇提出的问题展示出来，翻看了Python的文档，然后实现一番，果然在代码量上远远少于C语言，更加的形象，判若一个chat！好吧，附上代码和实验截图，一睹为快吧！

<!-- more -->

## 实现浏览器访问服务器

代码如下：

```

    from http.server import BaseHTTPRequestHandler,HTTPServer

    class RequestHandler(BaseHTTPRequestHandler):
        # ...页面模板...
        Page = '''\
        <html>
        <body>
        <table>
        <tr>  <td>Header</td>         <td>Value</td>          </tr>
        <tr>  <td>Date and time</td>  <td>{date_time}</td>    </tr>
        <tr>  <td>Client host</td>    <td>{client_host}</td>  </tr>
        <tr>  <td>Client port</td>    <td>{client_port}</td> </tr>
        <tr>  <td>Command</td>        <td>{command}</td>      </tr>
        <tr>  <td>Path</td>           <td>{path}</td>         </tr>
        </table>
        </body>
        </html>
        '''

        def do_GET(self):
            page = self.create_page()
            self.send_content(page)

        def create_page(self):
            values = {
                'date_time'   : self.date_time_string(),
                'client_host' : self.client_address[0],
                'client_port' : self.client_address[1],
                'command'     : self.command,
                'path'        : self.path
            }
            page = self.Page.format(**values)
            return page

        def send_content(self, page):
            self.send_response(200)
            self.send_header("Content-type", "text/html")
            self.send_header("Content-Length", str(len(page)))
            self.end_headers()
            self.wfile.write(page.encode('utf-8'))
            
    if __name__ == '__main__':
        serverAddress = ('', 8080)
        server = HTTPServer(serverAddress, RequestHandler)
        server.serve_forever()
```

在命令提示符窗口执行 *python server.py* ，然后打开浏览器，地址栏输入 *http://127.0.0.1:8080* ，浏览器中显示预先设定的显示内容，同时在命令提示符窗口显示访问成功的信息。如下图：

![server](https://img-blog.csdnimg.cn/20201220212401539.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3N0ZXZlbl96ZGc5ODg=,size_16,color_FFFFFF,t_70)

![browser](https://img-blog.csdnimg.cn/20201220212401474.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3N0ZXZlbl96ZGc5ODg=,size_16,color_FFFFFF,t_70)

## 实现服务器客户端实时交互

服务端代码：
```
    import socket
    import sys
    import time

    serversocket=socket.socket(socket.AF_INET,socket.SOCK_STREAM)
    host=socket.gethostname()#获取本地主机名
    port=9999
    #绑定端口号
    serversocket.bind((host,port))

    #设置最大连接数
    serversocket.listen(5)
    while True:
        print('服务器启动，监听客户端链接')
        clientsocket,addr=serversocket.accept()
        print('链接地址：%s' % str(addr))
        while True:
            try:
                data=clientsocket.recv(1024)
            except Exception:
                print('断开的客户端：',addr)
                break
            print('客户端发送内容：',data.decode('utf-8'))
            reply=input('回复：').strip()
            if not reply:
                break
            msg=time.strftime('%Y-%m-%d %X')#获取结构化时间戳
            msg1='[%s]:%s'% (msg,reply)
            clientsocket.send(msg1.encode('utf-8'))
        clientsocket.close()
    serversocket.closel()
```
以 *server-t.py*保存。

客户端代码：
```
    import socket
    import sys


    s=socket.socket(socket.AF_INET,socket.SOCK_STREAM)
    host=socket.gethostname()
    port=9999
    s.connect((host,port))
    while True:
        data= input('>>').strip()
        if not data:
            break
        s.send(data.encode('utf-8'))
        msg=s.recv(1024)
        if not msg:
            break
        print(msg.decode('utf-8'))
    s.close()
```
以 *client-t.py* 保存。

分别启动两个文件所在位置的命令提示符，执行 *python server-t.py*，*python client-t.py* ，此时两个命令提示符窗口如下图，你可以在两个窗口中分别输入信息然后回车就会发送到对应的交互端，类似于一个chat了！！！挺有意思，要不你也来试试？！:)

![server-t](https://img-blog.csdnimg.cn/20201220212401429.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3N0ZXZlbl96ZGc5ODg=,size_16,color_FFFFFF,t_70)

![client-t](https://img-blog.csdnimg.cn/20201220212401426.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3N0ZXZlbl96ZGc5ODg=,size_16,color_FFFFFF,t_70)

![logo](https://img-blog.csdnimg.cn/20201218081104595.jpg#pic_center)
