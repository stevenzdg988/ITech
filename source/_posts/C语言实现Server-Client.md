---
title: C语言实现Server-Client
date: 2020-12-20 13:48:39
tags: 
    - C/C++
    - Server-Client
    - Server-Browser
categories:
    - C语言
---

利用C语言实现客户端浏览器访问服务器和服务器与客户端交互两个场景。
<!-- more -->
## 模拟利用浏览器访问服务器

代码如下(Server-Browser)：

    #include <stdio.h>
    #include <stdlib.h>
    #include <string.h>
    #include <pthread.h>
    #include <sys/socket.h>
    #include <arpa/inet.h>
    #include <unistd.h>
    #include <signal.h>
    #include <sys/stat.h>
    #include <fcntl.h>

    /*
    *by GeekBear
    */

    #define PORT 8080
    //监听的端口号
    #define MAX_CONNECT 20
    最大连接数
    #define BUFFER_SIZE 1024*32
    //32k缓冲区满足绝大多数网页请求
    #define NAME_BUFFER_SIZE 1024
    //路径缓冲 
    #define HTTP_END "\r\n\r\n"
    //http协议头以两个回车换行结尾
    #define BASE_DIR "./100_www/"
    //服务器根目录设为当前程序运行路径
    #define DEFAULT_PAGE "/index.html"
    //默认页面
    #define HTTP_OK "HTTP/1.1 200 OK\r\n\r\n"
    //http正常回应


    static void *doAccept(void * parm);           

    //做协议解析 
    static void *doProc(void *data);

    int writeFile(int fd,char *filePath);

    int main()
    {
        pthread_t thread;
        printf("start!\n");
        printf("按回车键退出!\n");
        bzero(&thread,sizeof(thread));
        pthread_create(&thread,NULL,doAccept,NULL);
        getchar();
        printf("end!\n");
        return 0;
    }

    static void *doAccept(void * parm)
    {
        int socketfd,connectfd;
        pthread_t thread;
        struct sockaddr_in serverAddr;
        pthread_t pthread;
        signal(SIGCHLD,SIG_IGN);
        signal(SIGPIPE,SIG_IGN);
        socketfd = socket(AF_INET,SOCK_STREAM,0);
        if(socketfd == -1)
        {
            printf("创建套接字失败!\n");
        }
        else
        {
            printf("创建套接字成功!\n");
            bzero(&serverAddr,sizeof(serverAddr));
            serverAddr.sin_family=AF_INET;
            serverAddr.sin_addr.s_addr=htonl(INADDR_ANY);
            serverAddr.sin_port = htons(PORT);
            if( -1==bind(socketfd,(struct sockaddr*)&serverAddr,sizeof(struct sockaddr)) )
            {
                //FIXME 绑定端口失败时发生段错误！ 
                printf("绑定端口失败！\n");
            }
            else
            {
                printf("绑定端口成功！\n");
                if(-1==listen(socketfd,MAX_CONNECT))
                {
                    printf("创建监听失败！\n");
                }
                else
                {
                    printf("创建监听成功！\n");
                    printf("在浏览器中输入http://127.0.0.1:%d来访问本服务器\n",PORT);
                    while(1)
                    {
                        //sleep(1);
                        socklen_t socketLen=0;
                        connectfd = accept(socketfd,(struct sockaddr*)&serverAddr,&socketLen);
                        if(connectfd<=0)
                        {
                            printf("接受连接失败！\n");
                        }
                        else
                        {
                            printf("接受连接成功！\n");
                            bzero(&thread,sizeof(thread));
                            pthread_create(&thread,NULL,doProc,(void*)(intptr_t)connectfd);
                        }
                    }
                }
            }
            
        }
        return NULL;
    }

    //做协议解析 
    static void *doProc(void *data)
    {
        int connectfd = (int)(intptr_t)data;
        char *buffer = (char *)malloc(BUFFER_SIZE+1);
        if(buffer==NULL)
            printf("malloc return NULL!");
        int readLen=read(connectfd,buffer,BUFFER_SIZE);
        if(readLen<=0)
            goto END;
        buffer[readLen]=0;
        //printf("%s",buffer);
        int endLen = strlen(HTTP_END);
        if( (readLen>=endLen) && (strcmp(buffer+readLen-endLen,HTTP_END)==0) )
        {//是http协议
            printf("%s",buffer);
            char *startPos=strstr(buffer,"GET ");
            
            if(startPos!=NULL)
            {
                startPos+=4;
                char *endPos=strstr(startPos," ");
                if(endPos!=NULL&&(endPos-startPos)<NAME_BUFFER_SIZE)
                {
                    char *nameBuffer=malloc(NAME_BUFFER_SIZE+1);
                    if(nameBuffer==NULL)
                        printf("malloc return NULL!");
                    nameBuffer[0]=0;
                    strcat(nameBuffer,BASE_DIR);
                    strncat(nameBuffer,startPos,endPos-startPos);
                    if(nameBuffer[strlen(nameBuffer)-1]=='/')
                    {
                        printf("is_dir,use default page.\n");
                        strcat(nameBuffer,DEFAULT_PAGE);
                    }
                    /*
                    //判断路径是否为文件夹，不安全 
                    struct stat st;
                    stat(nameBuffer,&st);
                    if (S_ISDIR(st.st_mode))
                    {
                        printf("is_dir,use default page.\n");
                        strcat(nameBuffer,DEFAULT_PAGE);
                    }*/
                    printf("GET:%s\n",nameBuffer);
                    writeFile(connectfd,nameBuffer);
                    free(nameBuffer);
                }
            }
        }
    END:
        //printf("end doProc\n");
        free(buffer);
        close(connectfd);
        return NULL;
    }

    int writeFile(int fd,char *filePath)
    {
        //printf("write file:%s\n",filePath);
        int fileHandler=open(filePath,0,O_RDONLY);
        if(fileHandler<=0)
            return -1;
        char *buffer=(char *)malloc(BUFFER_SIZE+1);
        if(buffer==NULL)
            printf("malloc return NULL!");
        write(fd,HTTP_OK,strlen(HTTP_OK));
        int readLen=0;
        while( 0<(readLen=read(fileHandler,buffer,BUFFER_SIZE)) )
        {
            write(fd,buffer,readLen);
        }
        
        free(buffer);
        close(fileHandler);
        return 0;
    }

以上代码存储为server-browser.c，利用VSCode或命令行（**gcc server-broser.c -o hostt.exe**)进行编译生成可执行文件，Windows下为hostt.exe，打开系统下的命令提示符 *cmd* 将目录切换到程序所在目录，键入 *hostt.exe* 回车执行在窗口显示如下图所示：

![server-browser1](https://img-blog.csdnimg.cn/20201220152213579.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3N0ZXZlbl96ZGc5ODg=,size_16,color_FFFFFF,t_70)

然后打开浏览器按照提示信息将*http://127.0.0.1:8080*复制到浏览器的地址栏里访问，这时命令提示符窗口就会有反馈信息输出，代表模拟成功，如下图：

![server-browser2](https://img-blog.csdnimg.cn/20201220152213655.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3N0ZXZlbl96ZGc5ODg=,size_16,color_FFFFFF,t_70)

![server-browser3](https://img-blog.csdnimg.cn/20201220152213670.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3N0ZXZlbl96ZGc5ODg=,size_16,color_FFFFFF,t_70)

## 模拟客户端与服务端进行交互

首先创建服务端代码：

    #include <stdio.h>
    #include <stdlib.h>
    #include <string.h>
    #include <sys/socket.h>
    #include <arpa/inet.h>
    #include <signal.h>
    #include <unistd.h>


    /*
    *by GeekBear
    */


    #define PORT 8090
    //监听的端口号
    #define MAX_CONNECT 20
    //最大连接数

    int main()
    {
        char readBuffer[101];
        char writeBuffer[201];
        int socketfd,connectfd;
        struct sockaddr_in serverAddr;
        signal(SIGPIPE,SIG_IGN);
        //屏蔽管道破解信号，一般服务器都会屏蔽这个型号 
        socketfd = socket(AF_INET,SOCK_STREAM,0);
        //创建套接字 
        if(socketfd == -1)
        {
            printf("创建套接字失败!\n");
        }
        else
        {
            printf("创建套接字成功!\n");
            bzero(&serverAddr,sizeof(serverAddr));
            //相当于memset
            serverAddr.sin_family=AF_INET;
            //ipv4
            serverAddr.sin_addr.s_addr=htonl(INADDR_ANY);
            //设定监听的地址为任何地址都监听
            serverAddr.sin_port = htons(PORT);
            //设置端口号
            //套接字与端口和地址绑定
            if( -1==bind(socketfd,(struct sockaddr*)&serverAddr,sizeof(struct sockaddr)) )
            {
                printf("绑定端口失败！\n");
            }
            else
            {
                printf("绑定端口成功！\n");
                //创建监听
                if(-1==listen(socketfd,MAX_CONNECT))
                {
                    printf("创建监听失败！\n");
                }
                else
                {
                    printf("创建监听成功！\n");
                    printf("地址:127.0.0.1:%d\n",PORT);
                    while(1)
                    {
                        //sleep(1);
                        socklen_t socketLen=0;
                        //等待客户端的连接到达 
                        connectfd = accept(socketfd,(struct sockaddr*)&serverAddr,&socketLen);
                        if(connectfd<=0)
                        {
                            printf("接受连接失败！\n");
                        }
                        else
                        {
                            printf("接受连接成功！\n");
                            //接收数据 
                            int readLength=read(connectfd,readBuffer,100);
                            if(readLength>0)
                            {
                                readBuffer[readLength]=0;
                                printf("收到消息:%s,长度:%d\n",readBuffer,readLength);
                                sprintf(writeBuffer,"received your msg:'%s'\n",readBuffer);
                                //发送数据
                                write(connectfd,writeBuffer,strlen(writeBuffer));
                            }
                            //关闭与客户端的连接
                            close(connectfd);
                        }
                    }
                }
            }
            //FIXME 关闭套接字不知道是不是这个方法，反正感觉关闭失败了  
            close(socketfd);
        }
        printf("程序结束\n");
        return 0;
    }

然后创建客户端程序：

    #include <stdio.h>
    #include <string.h>
    #include <stdlib.h>
    #include <sys/socket.h>
    #include <arpa/inet.h>
    #include <unistd.h>

    /*
    *by GeekBear
    */

    int main()
    {
        char readBuffer[101];
        char writeBuffer[201];
        int connectRet,socketfd;
        struct sockaddr_in clientAddr;
        //signal(SIGPIPE,SIG_IGN);
        //屏蔽管道破解信号，一般服务器都会屏蔽这个型号 
        socketfd = socket(AF_INET,SOCK_STREAM,0);
        //创建套接字 ,AF_NET:ipv4，SOCK_STREAM:TCP协议 
        if(socketfd == -1)
        {
            printf("创建套接字失败!\n");
        }
        else
        {
            //return 0;
            printf("创建套接字成功!\n");
            bzero(&clientAddr,sizeof(clientAddr));//相当于memset
            clientAddr.sin_family=AF_INET;//ipv4
            //ip地址:0x7f000001:127.0.0.1
            //0x7f:127
            clientAddr.sin_addr.s_addr=htonl(0x7f000001);
            clientAddr.sin_port = htons(8090);
            //设置端口号
            connectRet=connect(socketfd,(struct sockaddr*)&clientAddr,sizeof(struct sockaddr));
            if(-1==connectRet)
            {
                printf("连接到服务器失败!\n");
            }
            else
            {
                printf("连接到服务器成功!\n");
                printf("请输入要发送的消息并回车:");
                scanf("%s",writeBuffer);
                write(socketfd,writeBuffer,strlen(writeBuffer));
                int readLength=read(socketfd,readBuffer,100);
                if(readLength>0)
                {
                    readBuffer[readLength]=0;
                    printf("收到的消息:%s\n",readBuffer);
                }
            }
            close(socketfd);
        }
        printf("程序结束\n");
        return 0;
    }

接下来将两个程序进行编译，分别生成 *server.exe* 和 *client.exe* ，分别打开2个命令提示符窗口，在其中一个执行 *server.exe* 在另一个执行 *client.exe* ，在执行 *client.exe*的窗口中输入信息，回车后会在执行服务端程序的窗口显示在 *client.exe* 窗口中输入的内容，进而实现了模拟服务器端与客户机端的信息交互过程。

如下图所示：

![server](https://img-blog.csdnimg.cn/20201220152213577.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3N0ZXZlbl96ZGc5ODg=,size_16,color_FFFFFF,t_70)

![client](https://img-blog.csdnimg.cn/20201220152213573.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3N0ZXZlbl96ZGc5ODg=,size_16,color_FFFFFF,t_70)

![interactive1](https://img-blog.csdnimg.cn/20201220152213576.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3N0ZXZlbl96ZGc5ODg=,size_16,color_FFFFFF,t_70)

![interactive2](https://img-blog.csdnimg.cn/20201220152213522.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3N0ZXZlbl96ZGc5ODg=,size_16,color_FFFFFF,t_70)

![interactive3](https://img-blog.csdnimg.cn/20201220152213520.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3N0ZXZlbl96ZGc5ODg=,size_16,color_FFFFFF,t_70)

![interactive4](https://img-blog.csdnimg.cn/20201220152213473.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3N0ZXZlbl96ZGc5ODg=,size_16,color_FFFFFF,t_70)

是不是很有趣？我觉着很有趣，下一步将用Python来实现一下这个过程，敬请期待。。。

![logo](https://img-blog.csdnimg.cn/20201218081104595.jpg#pic_center)
