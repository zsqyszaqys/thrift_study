## thrift
***
### 一.项目描述
### 游戏匹配服务

- 服务分为三部分：分别是game，match_system，save_server
- game为match_client端，通过match.thrift接口向match_system完成添加用户和删除用户的操作
- match_system由两部分组成，分别为match_server端和save_client端。match_server端负责接收match_client端的操作，将用户添加进匹配池，并且在匹配结束之后通过save.thrift接口将用户数据上传至另一个服务器
- save_server用于接收match_system上传的匹配成功的用户信息

#### 游戏匹配服务项目流程
- 构建match.thrift接口
- 通过match.thrift接口构建服务端和客户端
- 先将服务端和客户端跑通，能完成基本的连接通信
- 完成match.thrift的客户端需求
- 构建save.thrift接口
- 通过save.thrift接口构建服务端和客户端
- 将客户端业务添加到match_system当中，将save.thrift服务端完成（本项目save.thrift服务端已经完成）
- 根据业务需求完善match_system

### 二、项目完成过程
1. 在gitlab上创建项目，在teminal中创建thrift文件并将当前文件初始化为仓库  
2. 根据项目需求创建文件夹本项目中一共分为三个模块**game**，**match_system**，**thrift**  
3. 在thrift文件夹中创建match.thrift文件，登录thrift官方网站查看tutorial.thrift官方文档根据项目需求完成**match.thrift**，本项目需要定义三个部分
* 命名空间（cpp）
* 接口传入的用户信息（int 用户id，string 用户名，int 分数）
* match.thrift接口服务内容（添加用户，删除用户） 
4. 由thrift接口生成服务端，进入**match_system**文件夹中（最好多创建一个src文件夹）在src文件夹下执行thrift官方文档中`thrift -r --gen <language> <Thrift filename>`直接生成服务端，生成的文件为**gen.cpp**将其改名为**match_server**，进入**match_server**文件夹查看**Match_server.skeleton.cpp**为服务端代码，将其copy到src目录下并重命名为**main.cpp**  
5. 打开**main.cpp**，先在函数后面加上返回值，并编译文件（在工程中一般先将文件编译成功再加具体的逻辑），cpp文件编译包括两步，编译和链接
* 编译`g++ -c <filename>.cpp`
* 链接`g++ *.o -o main -lthrift`
* `./main`运行文件
6. 由thrift接口生成客户端，在**game**文件夹中创建src文件夹，执行`thrift -r --gen py tutorial.thrift`生成**gen.py**将其改名为**match_client**，查看文件中有服务器端文件**Match-remote**将其删除，因为目前只需要生成客户端（注意：在cpp中生成客户端此文件必须删除，因为cpp编译文件中只能有一个main函数）  
7. 在src目录下创建**client.py**，将官方文档中的client端代码复制到client.py中，注意修改头文件，执行`python3 <filename>`看看编译成功。如果编译成功则在代码中加上用户信息并调用服务端的函数，先启动服务端的main.cpp，再运行client.py看看服务端和客户端是否连接成功。修改代码使其能够读取终端中输入用户，编译运行如成功运行则match客户端完成
8. 修改服务端，具体修改逻辑参考<https://git.acwing.com/ycr2022/thrift/-/blob/master/match_system/src/main.cpp>
9. 在thrift文件夹目录下新建**save.thrift**，在y总目录中将内容复制过来，在src目录下同样执行`thrift -r --gen py tutorial.thrift`生成**gen.cpp**文件将其改名为**save.client**，进入文件夹将里面的.skeleton.cpp删除
10. 在**main.cpp**中实现save_client的功能，查看thrift官方文档，具体修改逻辑参考<https://git.acwing.com/ycr2022/thrift/-/blob/master/match_system/src/main.cpp>
11. 实现具体业务需求
* 将消费者模型升级为多线程4.0<https://git.acwing.com/ycr2022/thrift/-/commit/bd7a35d87fb1ca90cdae7a073b060f6f838f819b>
* 将匹配机制完善5.0，等待时间越长，阈值越大<https://git.acwing.com/ycr2022/thrift/-/commit/bd2ae34a66ec180f4b4f4da4f6ecd4a3810dc297>
### 三、知识点总结（根据git仓库history总结）
#### 构建match.thrift接口（match.thrift版本）
- 查询thrift官方文档<https://github.com/apache/thrift/blob/master/tutorial/tutorial.thrift>根据项目需求按照thrift文档语法要求定义命名空间，用户信息，接口服务内容
#### 通过match.thrift接口生成服务端（match_server:1.0版本）
- 查询thrift官方文档<https://thrift.apache.org/tutorial/cpp.html>根据项目需求生成服务端代码
- 端口：端口是一个16位的二进制数，其范围从0~65535。同一个端口只能由一个进程监听，当在一个端口号启动了一个服务，另一个进程将无法访问这个端口号。服务端的端口号和客户端的端口号要相同才能够连接
#### 通过match.thrift接口生成客户端（match_client:1.0版本）
- 查询thrift官方文档<https://thrift.apache.org/tutorial/py.html>根据项目需求生成客户端代码，调用服务端函数尝试能否连接
#### 完成match.thrift客户端（match_client:2.0 finish版本）
- python 从终端接收输入：`from sys import stdin`
#### 改进match.thrift服务端（match_server:2.0版本）
- 多线程thread：一个程序是一个进程，一个进程中至少有一个线程。如果只有一个线程，则第二个任务必须等到第一个任务结束后才能进行，如果使用多线程则在主线程执行任务的同时可以执行其他任务，而不需要等待。创建线程代价较小，但能有效提升cpu利用率。在本次项目中，我们需要输入用户信息和用户匹配是同时进行的，而不是输入用户信息结束才开始匹配，或匹配结束才能输入用户信息，所以我们需要开多线程编程。
- 生产者消费者模型：假如有两个线程A和B，A线程生产数据（类似本项目终端输入用户信息）并将信息加入缓冲区，B线程从缓冲区中取出数据进行操作（类似本项目中取出用户信息匹配），则A为生产者B为消费者。在多线程开发中，如果生产者生产数据的速度很快，而消费者消费数据的速度很慢，那么生产者就必须等待消费者消费完数据才能够继续生产数据，因为生产过多的数据可能会导致存储不足；同理如果消费者的速度大于生产者那么消费者就会经常处理等待状态，所以为了达到生产者和消费者生产数据和消费数据之间的平衡，那么就需要一个缓冲区用来存储生产者生产的数据，所以就引入了生产者-消费者模型。当缓冲区满的时候，生产者会进入休眠状态，当下次消费者开始消耗缓冲区的数据时，生产者才会被唤醒，开始往缓冲区中添加数据；当缓冲区空的时候，消费者也会进入休眠状态，直到生产者往缓冲区中添加数据时才会被唤醒  
-- 在本项目头文件中加入`#include <thread>`引入多线程
- 消息队列message_queue：在生产者消费者模型中我们提到了缓冲区，缓冲区的实现就是由队列来实现，当生产者生产数据后将信息入队，消费者获取信息后信息出队。消息队列提供了异步通信协议，也就是说，消息的发送者和接收者不需要同时与消息队列交互，消息会保存在队列中，直到接收者使用它  
-- 在本项目中手动实现消息队列，在头文件中加入`#include <queue>`，定义一个结构体将互斥锁mutex，队列queue和条件变量condition加入结构体即可
- 互斥锁mutex：保证共享数据操作的完整性，保证在任一时刻只能有一个线程访问对象。锁有两个操作。一个P操作(上锁)，一个V操作(解锁)。P和V都是原子操作，就是在执行P和V操作时，不会被插队。锁一般使用信号量来实现的，mutex其实就是信号量=1。互斥量就是同一时间能够分给一个人，即S=1。S=10表示可以将信号量分给10个人来用。如果一共有20个人那么只能有10个人用，剩下10个人需要等待。  
-- 在本项目中有两个操作添加用户和删除用户，信息都是存在消息队列当中，如果不上锁，这两个操作同时执行可能导致在消息队列当中信息错乱。在本项目头文件中加入`#include <mutex>`引入互斥锁
- 条件变量condition_variable：条件变量一般和互斥锁搭配使用，条件变量用于在多线程环境中等待特定事件发生。  
-- 在本项目中如果消息队列为空则等待，如果有添加用户和删除用户的操作则将消息队列唤醒
#### 完成save.thrift服务端（implement save_client版本）
- 上传数据到服务器需要输入服务端的用户名和密码，密码可以用md5值加密
- 求一个字符串的md5值：终端输入命令`md5sum`，输入原密码回车然后Ctrl+d


