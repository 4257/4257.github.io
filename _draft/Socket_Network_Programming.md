## socket 网络编程
### linux socket 函数
```c
#include <sys/socket.h>


int socket(int domain,int type,int protocol);

int bind(int sockfd, struct sockaddr* myaddr, socklen_t addrlen);

int listen(int sockfd, int backlog);

int accept(int sockfd,struct sockaddr* addr,socklen_t* addrlen);

int connect(int sockfd,struct sockaddr* serv_addr,socklen_t addrlen);

//成功返回0 失败返回-1
```
```
struct sockaddr_in{
  sa_family_t     sin_family;
  uint16          sin_port;
  struct adr_in   sin_addr;  //uint32_t
  char            sin_zero[8];
}

struct sockaddr{
  sa_family_t     sin_family;
  char            sa_data[14];//这里实际需要存储的是sockaddr_in里面sin_port + sin_addr的数据 剩下的字节全部填充0
}
```
//创建socket 随后声明并且初始化地址信息结构体变量
- socket():
  - domian:  协议族信息
    - PF_INET    IPv4协议族
    - PF_INET6   IPv6协议族
    - PF_LOCAL   本地通信的UNIX协议族
    - PF_PACKET  底层套接字的协议族
    - PF_IPX     IPX NoVell协议族
  - type：   数据传输类型
    - SOCK_STREAM  面向连接的套接字
    - SOCK_DGRAM   面向消息的套接字
  - protocol: 计算机间通信中使用的协议信息
    - TCP 0
    - UDP 1
//向套接字分配地址
- bind():
  - sockfd     socket描述符
  - myaddr     需要一个struct sockaddr*的变量一般将sockaddr_in转成sockaddr
  - addrlen    地址长度sockaddr等于sockaddr_in
//进入等待连接请求状态
- listen()
  - sockfd socket描述符,传入需要进入等待连接请求状态的socket描述符以成为服务器端socket
  - backlog  连接请求队列的长度
//受理客户端连接请求
- accept()
  - sockfd  服务器端socket描述符
  - addr    保存发起连接请求的客户端地址信息的变量地址
  - addrlen  第二个参数长度的地址
//客户端的IP、端口在调用connect时自动分配 无需使用bind函数进行分配
-connect()
  - sockfd  客户端套接字描述符
  - servaddr  保存的是目标服务端地址信息的变量地址值
  - addrlen    第二个结构体变量的地址
### windows socket 函数
```c
WSADATA wsaData;
WSAStartup(MAKEWORD(2, 2), &wsaData);

SOCKET ListenSocket;
ListenSocket = socket(AF_INET, SOCK_STREAM, IPPROTO_TCP);

sockaddr_in service;
service.sin_family = AF_INET;
service.sin_port = htons(27015);
inet_pton(AF_INET, "127.0.0.1", &service.sin_addr);

bind(ListenSocket,(SOCKADDR *) & service, sizeof (service)
//将本地地址与套接字关联

listen(ListenSocket, 1)
//将套接字置于侦听传入连接的状态

SOCKET AcceptSocket;
AcceptSocket = accept(ListenSocket, NULL, NULL);
//允许套接字尝试传入连接

closesocket(ListenSocket);

WSACleanup();
```
