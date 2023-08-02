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
