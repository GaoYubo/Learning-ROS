# ROS多机通信

使用ROS时，一个相当典型的设置是在机器人上安装一台笔记本电脑或单板机，同时在台式机上监视其动作。ROS使得多台机器相对容易地查看同一组主题、服务和参数。当机器人的板载计算机不是很强大时，这是非常有用的，因为它允许您在桌面上运行更苛刻的进程，比如RViz。(RViz等程序也能很快耗尽笔记本电脑的电池。)  

## 联机前的准备工作  
### 查看ip地址  
两台需要通信的设备，必须要连接在同一个局域网。通过命令查看ip地址。
>$ ifconfig  

确定运行主节点master的设备，该设备的IP地址就是ROS_MASTER_URI。  
### 检查两台设备是否连通  
通过命令，两台设备互相ping，对方的ip地址，确定是否能够互相访问。  
[linux虚拟机可以ping通树莓派，树莓派无法ping通虚拟机](https://github.com/GaoYubo/Learning-Linux/blob/master/Notes/linux%E8%99%9A%E6%8B%9F%E6%9C%BA%E5%8F%AF%E4%BB%A5ping%E9%80%9A%E6%A0%91%E8%8E%93%E6%B4%BE%EF%BC%8C%E6%A0%91%E8%8E%93%E6%B4%BE%E6%97%A0%E6%B3%95ping%E9%80%9A%E8%99%9A%E6%8B%9F%E6%9C%BA.md)
### 用netcat进一步检查  
因为ping只检查ICMP包在设备之间是否可以访问，这还不够。您需要确保能够通过所有端口进行通信。这很难完全检查，因为您必须遍历大约65K个端口。  
我们可以使用netcat选择任意的端口进行通信测试。一定要选择大于1024的端口，1024以下的端口需要超级用户权限。注意，netcat可执行文件在某些发行版上可能被命名为nc。
>主机端  
 $ netcat -l 1234  
 笔记本端  
 $ netcat hostname 1234  

 然后你就可以像类似于QQ聊天界面一样，在两个窗口间互发消息。  
 接着换个方向试试，反向试试。  
 如果两边都成功了，那么即可确定两台设备能够正常的进行通信了。

### 编辑环境变量  
编辑对应的ROS环境变量，ROS_HOSTNAME和ROS_MASTER_URI。  
注意这一步很重要！！！  
**ROS_HOSTNAME**  
它代表需要运行ROS节点的机器的ip地址。(每台设备独立的)  
**ROS_MASTER_URI**  
它代表主节点master的地址，也就是运行roscore的设备的地址。(多台设备公用的，唯一的地址)  
示例如下：  
![ROS_MASTER_URI](https://github.com/GaoYubo/Learning-ROS/tree/master/pictures/ros_master_url.png)


## 同步时间  
在ROS网络中，机器之间的时间同步通常很关键，因为帧转换和许多消息类型都有时间戳。保持计算机同步的一个简单方法是在桌面和机器人上安装Ubuntu chrony包。这个包将使计算机时钟与Internet服务器保持同步，从而实现彼此同步。  

> $ sudo apt-get install chrony  

安装完成后，chrony守护进程将自动启动并开始与多个Internet服务器同步您的计算机时钟。

## 开始测试  
在主机端运行roscore启动master节点，并且发布一个话题，然后在机器端订阅该话题，即可看到打印内容。  
反向测试  
更改ROS_MASTER_URI为机器端地址，运行roscore启动master节点，并且发布一个话题，在主机端订阅该话题，即可看到打印内容。  
如果两次测试都正常打印输出内容，那么恭喜你，你已经实现了ROS的多机通信。

## 参考文献  

[1. ROSNetworkSetup](http://wiki.ros.org/ROS/NetworkSetup)  
[2. ROS在多机器人上的使用](http://wiki.ros.org/cn/ROS/Tutorials/MultipleMachines)


