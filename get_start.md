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
(6)控制移动平台移动:
```
rostopic pub -r 0.5 /cmd_vel geometry_msgs/Twist '{linear: {x: 0.00, y: 0, z: 0}, angular: {x: 0, y: 0, z: 0.0}}' 
```
可以控制移动平台的运动,到这里跟运动平台的通讯就完成
# 二.启动ridgeback_viz仿真界面
ridgeback_viz包在/ridgeback/ridgeback_desktop路径下，下载链接：[click]()

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
