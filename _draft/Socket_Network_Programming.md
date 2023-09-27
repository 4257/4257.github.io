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
 
### 常用函数
read()
read函数会尽可能的从socket中获得更多数据  
如果在服务端接收客户端发送的数据时 调用两次read函数 则第二次调用的read并不会获得第一次获得的数据
```
client
write buff = "1234567890"
server
read(socket,buff,3) -> buff = "123"
read(socket,buff,7) -> buff = "4567890"
```
### TCP
TCP套接字中的I/O缓冲  
在创建套接字时 操作系统会分配发送缓冲和接收缓冲  
当send时 数据被复制到发送缓冲 直到操作系统决定将其发送到目标套接字的接收缓冲中
当数据被传输到套接字时 首先会被存储在接受缓冲中 并等到城市调用recv来读取
TCP中的服务端套接字指的是accept()函数接受来自客户端的连接请求时 操作系统为每个客户端创建的新套接字
bind()函数用来绑定socket与地址信息
TCP客户端套接字在调用connect函数时自动分配IP地址和端口(随机)

基于TCP的半关闭

### UDP
UDP客户端套接字可以调用bind函数绑定IP地址和端口也可以等sendto的时候自动分配IP地址和端口(随机)
UDP套接字存在数据边界 一端sendto了多少次 一端就要recvfrom多少次 
UDP一个数据包即可算是一个完整数据 也称数据报
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
