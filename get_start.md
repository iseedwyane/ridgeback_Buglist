# 零、ROS.wiki download the package
[wiki.ros.org/ridgeback_viz](http://wiki.ros.org/ridgeback_viz)
```
ssen@sen-inspiron-15-7000-gaming:~$ mkdir -p ridgeback_ws/src
ssen@sen-inspiron-15-7000-gaming:~$ cd ~/ridgeback_ws/src
ssen@sen-inspiron-15-7000-gaming:~/ridgeback_ws/src$ git clone https://github.com/ridgeback/ridgeback_desktop.git
正克隆到 'ridgeback_desktop'...
remote: Enumerating objects: 191, done.
remote: Total 191 (delta 0), reused 0 (delta 0), pack-reused 191
接收对象中: 100% (191/191), 27.40 KiB | 14.00 KiB/s, 完成.
处理 delta 中: 100% (90/90), 完成.
检查连接... 完成。
ssen@sen-inspiron-15-7000-gaming:~/ridgeback_ws/src$ 
ssen@sen-inspiron-15-7000-gaming:~/ridgeback_ws/src$ cd ..
ssen@sen-inspiron-15-7000-gaming:~/ridgeback_ws$ catkin_make
```
## BUG1
```
ssen@sen-inspiron-15-7000-gaming:~$ roslaunch ridgeback_viz view_robot.launch
[view_robot.launch] is neither a launch file in package [ridgeback_viz] nor is [ridgeback_viz] a launch file name
The traceback for the exception was written to the log file
ssen@sen-inspiron-15-7000-gaming:~$ 
```
缺路径环境配置；
```
ssen@sen-inspiron-15-7000-gaming:~$ echo "source ~/ridgeback_ws/devel/setup.bash" >> ~/.bashrc
ssen@sen-inspiron-15-7000-gaming:~$ echo $ROS_PACKAGE_PATH 
/home/ssen/sensor_ws/src:/home/ssen/ur5_ws/src:/opt/ros/kinetic/share
ssen@sen-inspiron-15-7000-gaming:~$ source .bashrc 
ssen@sen-inspiron-15-7000-gaming:~$ echo $ROS_PACKAGE_PATH 
/home/ssen/ridgeback_ws/src:/home/ssen/sensor_ws/src:/home/ssen/ur5_ws/src:/opt/ros/kinetic/share
ssen@sen-inspiron-15-7000-gaming:~$ 
```
注：
立即生效（此时应该在家目录下）source .bashrc
注：如果不执行 source 命令，则需重启系统才能生效。
可以通过：
```
have a look:
$ gedit .bashrc
$ sudo gedit .bashrc
```
但是在PC的RVIZ没有看到车，因为导入的不是仿真模型吧？需要连上实体机器人，我去试试。

# 一.[跟ridgeback通讯上](https://github.com/GJXS1980/Lab409_Ridgeback):

(1)在自己电脑创建有线连接,自己配置的IP  
```
ssen@sen-inspiron-15-7000-gaming:~$ ifconfig
enp2s0    Link encap:以太网  硬件地址 50:9a:4c:c1:65:d0  
          inet 地址:192.168.131.39  广播:192.168.131.255  掩码:255.255.255.0
          inet6 地址: fe80::320:af0b:aae1:b6f4/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:1500  跃点数:1
          接收数据包:69390 错误:0 丢弃:0 过载:0 帧数:0
          发送数据包:36224 错误:0 丢弃:0 过载:0 载波:0
          碰撞:0 发送队列长度:1000 
          接收字节:32861988 (32.8 MB)  发送字节:4620359 (4.6 MB)
```

(2)通过ssh连接上移动平台
[先前的例子](https://zhuanlan.zhihu.com/p/45121106)
```
3.sudo vim ~/.bashrc 或者 vim .bashrc
（在odroid无人机这端）
加入 export ROS_IP=Odoird_IP; 
将Odoird_IP;替换成 $ ifconfig 后查到的ip地址
4.在PC端，
Odoird_IP;
加入  
export ROS_IP=PC_IP;
export ROS_MASTER = http://odroid_ip:11311;
现在先在主机roscore，从机（PC）就可以通过rostopic list 订阅到主机（odroid）的话题了
5.在PC上登陆odroid
$ ssh odroid@192.168.63.33
名字由$ hostname知
ip是odroid的ip
后输入密码即可
```
但是在这个ridgeback例子中，
底盘端的系统应该是配置好了，因为它需要提供它的ip给我们就可以了。
我们PC端：打开
```
gedit .bashrc
```
并写入
```
export ROS_IP=1921,68.131.39;
export ROS_MASTER_URI=http://192.168.131.1:11311；
```
# 科普一下.bashrc和/etc/hosts文件
```
一般会有多个 .bashrc 文件，使用 find 命令可以查看：
$ sudo find / -name .bashrc


/etc/hosts，主机名和ip配置文件
这个文件告诉主机那些域名对应那些ip,哪些主机名对应哪些ip
比如文件中有这样的定义
192.168.1.100    linumu100    test100
假设192.168.1.100是一台网站服务器，在网页中输入http://linumu100或http://test1000就会打开192.168.1.100的网页。
```
(3)更改.bashrc
```
export ROS_MASTER_URI=http://192.168.131.1:11311 #ridegback的IP地址
export ROS_IP=192.168.31.50 #你电脑的IP地址
```
使环境生效,在终端输入:
```
source .bashrc
```
(4)打开终端,修改/etc/hosts文件
```
sudo gedit /etc/hosts
```
加入下面代码:
```
192.168.131.1 CPR-R100-0038 #创建有线连接移动平台的IP 名字

192.168.131.50 GJXS #创建有线连接你电脑的IP 名字
```
(5)查询话题列表
```
rostopic list
```
可以看到移动平台的话题 
##BUG2
```
ssen@sen-inspiron-15-7000-gaming:~$ rostopic list
Traceback (most recent call last):
  File "/opt/ros/kinetic/bin/rostopic", line 35, in <module>
    rostopic.rostopicmain()
  File "/opt/ros/kinetic/lib/python2.7/dist-packages/rostopic/__init__.py", line 2099, in rostopicmain
    _rostopic_cmd_list(argv)
  File "/opt/ros/kinetic/lib/python2.7/dist-packages/rostopic/__init__.py", line 2039, in _rostopic_cmd_list
    exitval = _rostopic_list(topic, verbose=options.verbose, subscribers_only=options.subscribers, publishers_only=options.publishers, group_by_host=options.hostname) or 0
  File "/opt/ros/kinetic/lib/python2.7/dist-packages/rostopic/__init__.py", line 1203, in _rostopic_list
    master = rosgraph.Master('/rostopic')
  File "/opt/ros/kinetic/lib/python2.7/dist-packages/rosgraph/masterapi.py", line 100, in __init__
    self._reinit(master_uri)
  File "/opt/ros/kinetic/lib/python2.7/dist-packages/rosgraph/masterapi.py", line 118, in _reinit
    raise ValueError("invalid master URI: %s"%(master_uri))
ValueError: invalid master URI: http://192.168.131.1:11311；
ssen@sen-inspiron-15-7000-gaming:~$ 
```
解决方式：
是因为忘了选择“在自己电脑创建有线连接,自己配置的IP ”
```
ssen@sen-inspiron-15-7000-gaming:~$ rostopic list
/bluetooth_teleop/joy
/cmd_lights
/cmd_vel
/diagnostics
/diagnostics_agg
/diagnostics_toplevel_state
/feedback
/front/scan
/imu/data
/imu/data_raw
/imu/mag
/imu_filter/parameter_descriptions
/imu_filter/parameter_updates
/joint_states
/laser_status
/mcu/cmd_fans
/mcu/cmd_lights
/mcu/status
/odometry/filtered
/ridgeback_velocity_controller/cmd_vel
/ridgeback_velocity_controller/odom
/rosout
/rosout_agg
/set_pose
/status
/tf
/tf_static
/twist_marker_server/feedback
/twist_marker_server/update
/twist_marker_server/update_full
/urg_front/parameter_descriptions
/urg_front/parameter_updates
/wifi_connected
```

(6)控制移动平台移动:
```
rostopic pub -r 0.5 /cmd_vel geometry_msgs/Twist '{linear: {x: 0.00, y: 0, z: 0}, angular: {x: 0, y: 0, z: 0.0}}' 
```
可以控制移动平台的运动,到这里跟运动平台的通讯就完成
# 二.启动ridgeback_viz仿真界面
ridgeback_viz包在/ridgeback/ridgeback_desktop路径下，下载链接：[click](https://github.com/iseedwyane/ridgeback_desktop),来源于ros.wiki

(1)导入模型
```
roslaunch ridgeback_viz view_robot.launch
```
##BUG3
```
ssen@sen-inspiron-15-7000-gaming:~$ roslaunch ridgeback_viz view_robot.launch
... logging to /home/ssen/.ros/log/54b2acb6-ff00-11e9-bb51-000babe07c17/roslaunch-sen-inspiron-15-7000-gaming-25623.log
Checking log directory for disk usage. This may take awhile.
Press Ctrl-C to interrupt
Done checking log file disk usage. Usage is <1GB.

Unable to contact my own server at [http://1921,68.131.39:39721/].
This usually means that the network is not configured properly.

A common cause is that the machine cannot ping itself.  Please check
for errors by running:

	ping 1921,68.131.39

For more tips, please see

	http://www.ros.org/wiki/ROS/NetworkSetup

The traceback for the exception was written to the log file

```
新开终端
```
roslaunch ridgeback_viz view_model.launch
```
(2)显示激光雷达和imu
添加激光雷达(LaserScan)和imu
激光雷达(LaserScan)的topic设置为:/front/scan
imu的topic设置为:/imu/data_raw

(3)查看电机运行情况
```
rosrun rqt_robot_monitor rqt_robot_monitor
```
