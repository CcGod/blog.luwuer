注意：本文大部分内容都摘抄于《图解 HTTP》，就不一一使用引用标记了。

# HTTP 的诞生

1989年3月，互联网还只属于少数人。在这一互联网的黎明期，HTTP诞生了。

CERN（欧洲核子研究组织）的蒂姆·伯纳斯-李（Tim Berners-Lee）博士提出了一种能让远隔两地的研究者们共享知识的设想。最初设想的基本理念是：借助多文档之间相互关联形成的超文本（HyperText），连成可相互参阅的WWW（World Wide Web，万维网）。

现在已提出了3项 WWW 构建技术，分别是：把 SGML（Standard Generalized MarkupLanguage，标准通用标记语言）作为页面的文本标记语言的HTML（HyperText MarkupLanguage，超文本标记语言）；作为文档传递协议的 HTTP；指定文档所在地址的URL（UniformResource Locator，统一资源定位符）。

WWW 这一名称，是 Web 浏览器当年用来浏览超文本的客户端应用程序时的名称，现在则用来表示这一系列的集合，也可简称为 Web。

# TCP/IP 协议簇

关于 TCP/IP 有两种认知：
1. TCP/IP 是指 TCP 和 IP 这两种协议
2. TCP/IP 是在 IP 协议的通信过程中，使用到的协议族的统称

## TCP/IP 分层管理

由上至下分别是：
1. 应用层，比如：FTP（File Transfer Protocol，文件传输协议、 DNS（Domain Name System，域名系统、 HTTP
2. 传输层，比如：TCP（Transmission Control Protocol，传输控制协议）和UDP（User Data Protocol，用户数据报协议）
3. 网络层（又名网络互连层），比如： IP（Internet Protocol，网际协议）
4. 链路层（又名数据链路层，网络接口层），用来处理连接网络的硬件部分，比如： 操作系统、硬件的设备驱动、网卡、光纤

