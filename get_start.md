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
可以通过：
```
have a look:
$ gedit .bashrc
$ sudo gedit .bashrc
```
但是在PC的RVIZ没有看到车，因为导入的不是仿真模型吧？需要连上实体机器人，我去试试。

# 一.[跟ridgeback通讯上](https://github.com/GJXS1980/Lab409_Ridgeback):

(1)在自己电脑创建有线连接
(2)通过ssh连接上移动平台
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
