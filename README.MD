A Webserver with c++
====
![](https://img.shields.io/badge/language-c++-green.svg)

Introduction
----
本项目为C++11写的基于http协议的Web服务器，写这个的目的是为了让半年来看的东西学以致用．
测试
远程

Environment
----
* OS:Ubuntu 16.04.4
* Compiler: g++ 4.8
* openssl

## Build

	make&&sudo make install

## Usage

	安装路径 /usr/local/WebServer
	sudo ./WebServer
	前台运行可通过ctrl c安全关闭程序
	后台程序可通过kill -QUIT安全关闭程序
	静态网页挂在page

	
## Run in Docker

### Build
```sh
git clone https://github.com/viktorika/Webserver.git && cd Webserver
docker build -t vikserver .
```
or Build by URL:
```sh
docker build -t vikserver https://github.com/viktorika/Webserver.git
```

### Run
```sh
# Apply you own config file
docker run \
	-dit \
	--rm \
    -p 80:8080 \
	-v /PATH/TO/YOUR.conf:/opt/conf/Webserver.conf \
	-v /PATH/TO/WEBPAGE/:/opt/page/ \
	--name=vikserver
	vikserver
```

Or Pull from [Dockerhub](https://hub.docker.com/r/visnz/vikserver): ``docker pull visnz/vikserver``

Or Run by ``docker-compose.yaml``:
```sh
docker-compose up
```
	
Technical points
----
* 采用Reactor模式+EPOLL(ET)非阻塞IO
* 使用小根堆+unordered_map实现定时器以关闭超时连接
* 参考muduo的one loop per thread的思想
* 使用了智能指针，bind和function等c++11的新特性
* 用状态机解析了http请求，写法较优雅．可以参考一下
* 使用RAII机制封装锁，让线程更安全
* 优化blocking queue,子线程在执行主线程给的任务时，把emptyqueue置换给pendingfunctorqueue，之后对emptyqueue进行操作就可以了，这样可以减少临界区的代码，在主线程accept往子线程添加任务时反应更迅速．
* 参考muduo用双缓冲技术实现Log日志.
* 实现自己的内存池，进一步优化性能
* 实现了LFU缓存，加快访问速度
* 通过openssl接口支持ssl协议
