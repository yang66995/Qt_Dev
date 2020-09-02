# Qt Network

## Network Programming with Qt 

The Qt Network module offers classes that allow you to write TCP/IP clients and servers. It offers lower-level classes such as QTcpSocket, QTcpServer and QUdpSocket that represent low level network concepts, and high level classes such as QNetworkRequest, QNetworkReply and QNetworkAccessManager to perform network operations using common protocols. It also offers classes such as QNetworkConfiguration, QNetworkConfigurationManager and QNetworkSession that implement bearer management. 

## Using UDP with QUdpSocket

![wpjJED.png](https://s1.ax1x.com/2020/09/02/wpjJED.png)

UDP (User Datagram Protocol) is a lightweight, unreliable, datagram-oriented, connectionless protocol. It can be used when reliability isn't important. For example, a server that reports the time of day could choose UDP. If a datagram with the time of day is lost, the client can simply make another request.//UDP(用户数据报协议)是一种轻量级的、不可靠的、面向数据的、无连接的协议。当可靠性不重要时，可以使用它。例如，报告一天时间的服务器可以选择UDP。如果一个包含时间的数据报丢失了，客户端可以简单地发出另一个请求。

QUdpSocket类允许您发送和接收UDP数据报。它继承QAbstractSocket，因此它共享QTcpSocket的大部分接口。主要区别在于，QUdpSocket以数据报的形式传输数据，而不是以连续的数据流的形式传输数据。简而言之，数据报是一个大小有限的数据包(通常小于512字节)，除了传输的数据外，还包含数据报发送方和接收方的IP地址和端口。

QUdpSocket支持IPv4广播。广播通常用于实现网络发现协议，例如查找网络上哪台主机拥有最多的空闲硬盘空间。一台主机向网络广播所有其他主机接收的数据报。每个接收到请求的主机然后用其当前的空闲磁盘空间量向发送方发送一个回复。发起者将等待，直到它收到来自所有主机的响应，然后可以选择拥有最多空闲空间的服务器来存储数据。要广播数据报，只需将其发送到特殊地址QHostAddress:: broadcast(255.255.255.255)，或发送到您的本地网络的广播地址。

bind()准备套接字以接受传入的数据报，与TCP服务器的QTcpServer::listen()非常类似。每当一个或多个数据报到达时，QUdpSocket就会发出readyRead()信号。调用QUdpSocket::readDatagram()来读取数据报。

QUdpSocket还支持多播。