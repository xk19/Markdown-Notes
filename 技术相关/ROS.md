### 通讯方式

- ‌**话题（Topics）**‌：发布/订阅（Publish/Subscribe）通信机制，允许一个节点发布消息（Message），其他节点可以订阅这些消息。话题适用于实时数据传输，如传感器数据。
- **服务（Services）**‌：请求/响应（Request/Response）通信机制，客户端（Client）发送一个请求到服务端，服务端（Server） 处理后返回结果。服务适用于需要交互和响应的场景，如设置参数或查询状态。

### 常用命令

- 查看ros命令：命令行输入ros,按tab键

- ros安装位置：/opt/ros/

- 创建工作空间：mkdir -p ~/catkin_ws/src

​				  cd ~/catkin_ws/src

​				  catkin_init_workspace

- 编译工作空间：cd ~/catkin_ws

​                                   catin_make

- 设置环境变量：source devel/setup.bash

- 检查环境变量：echo $ROS_PACKAGE_PATH

- 创建功能包：cd ~/catkin_ws/src

​                              catkin_creat_pkg+packagename+depends（常用std_msgs、rospy、roscpp）

- 编译功能包：cd ~/catkin_ws

​			       catkin_make

​			       source ~/catkin_ws/devel_setup.bash