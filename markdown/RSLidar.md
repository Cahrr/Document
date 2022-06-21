---
typora-root-url: ..\picture
---

#  实验室无人机平台及相关应用

## 无人机平台

#### 2.1 RoboSense RS-Helios 32线激光雷达配置与使用

##### 2.1.1 网口配置激光雷达

- 安装wireshark

  根据文档安装并查看IP地址，配置电脑IP地址并ping通，记录激光雷达IP地址。

  ![](https://cnmafia.oss-cn-beijing.aliyuncs.com/Typora_img/202206211815918.png)

- 安装RSView

  根据文档安装RSView，网口连接激光雷达，点击左上方雷达图标，选择对应型号查看点云。

  ![](https://cnmafia.oss-cn-beijing.aliyuncs.com/Typora_img/202206211816707.png)
  
  https://www.robosense.cn/rslidar/RS-Helios
  

##### 2.1.2 配置ROS开发环境

- 下载rslidar_sdk

  下载地址：https://github.com/RoboSense-LiDAR/rslidar_sdk/releases/download/v1.3.0/rslidar_sdk.tar.gz

  ![](https://cnmafia.oss-cn-beijing.aliyuncs.com/Typora_img/202206211831997.png)
  
  下载此rslidar_sdk.tar.gz 文件，因为此文件内容齐全，不需要再下载子模块rs_driver的代码。
  
  保存至`/catkin_ws/src`路径下。

- 安装环境依赖

  安装Yaml

  ```shell
  sudo apt-get update
  sudo apt-get install -y libyaml-cpp-dev
  ```

  安装Pcap

  ```shell
  sudo apt-get install -y  libpcap-dev
  ```

  安装Protobuf

  ```shell
  git clone https://github.com/protocolbuffers/protobuf.git
  sudo apt-get install autoconf  automake  libtool curl make  g++  unzip libffi-dev -y
  cd protobuf/ 
  ./autogen.sh 
  ./configure
  # 编译时间较长
  make 
  sudo make install
  sudo ldconfig 
  protoc -h 
  ```

- 配置参数

  `/rslidar_sdk/CMakeLists.txt`

  ```cmake
  set(COMPILE_METHOD ORIGINAL) 改为 set(COMPILE_METHOD CATKIN)
  ```

  ```cmake
  set(POINT_TYPE XYZI) 改为 set(POINT_TYPE XYZIRT) 
  ```

  将`/rslidar_sdk/package_ros1.xml`重命名为 `package.xml`

  `/rslidar_sdk/config/config.yaml`

  ```yaml
  lidar:
  	- driver:
  		//此处修改激光雷达型号
  		lidar_type: RSHELIOS    #LiDAR type - RS16, RS32, RSBP, RS128, RS128_40, RS80, RSM1, RSHELIOS
  		frame_id: /rslidar           
  		msop_port: 6699             
  		difop_port: 7788  
  		// 下面还可根据需要修改rostopic名称
  ```

- 编译安装

  ```shell
  catkin_make
  source devel/setup.bash
  ```

- 运行雷达显示原始格式点云

  ```shell
  roslaunch start.launch
  ```

##### 2.1.3 将rslidar点云数据转换为Velodyne点云格式

- 下载源码

  ```shell
  git clone https://github.com/HViktorTsoi/rs_to_velodyne.git
  ```

- 添加launch文件

  新建`/rs_to_velodyne/launch`文件夹，新建`rs_to_velodyne.launch`文件，内容如下。

  LeGO-LOAM需要的点云格式为XYZIR，`args="XYZIRT XYZIR"`为转换格式的参数。
  
  ```html
   <launch>
    <node pkg="rs_to_velodyne" name="rs_to_velodyne" type="rs_to_velodyne"  args="XYZIRT XYZIR"   output="screen">
    </node>
  </launch>
  ```
  
- 编译安装

  ```shell
  cd ~/catkin_ws
  catkin_make
  source devel/setup.bash
  ```

- 运行节点

  ```shell
  roslaunch rs_to_velodyne rs_tovelodyne.launch
  ```

  订阅topic：`/rslidar_points`，发布topic： `/velodyne_points`

  https://blog.csdn.net/weixin_44023934/article/details/123845089



#### 	2.2 LeGO-LOAM 算法激光雷达建图

##### 2.2.1 依赖安装

- 安装 gtsam

  ```shell
  # 第一步下载有问题可直接访问网址下载，继续后续步骤
  wget -O ~/Downloads/gtsam.zip https://github.com/borglab/gtsam/archive/4.0.0-alpha2.zip
  cd ~/Downloads/ && unzip gtsam.zip -d ~/Downloads/
  cd ~/Downloads/gtsam-4.0.0-alpha2/
  mkdir build && cd build
  cmake ..
  # 编译安装时间较长，耐心等待
  sudo make install
  ```

##### 2.2.2 LeGO-LOAM 安装

- 源码下载与编译

  ```shell
  cd ~/catkin_ws/src
  git clone https://github.com/RobustFieldAutonomyLab/LeGO-LOAM.git
  cd ..
  # 必须使用 -j1 参数，否则报错
  catkin_make -j1
  # 添加工作空间环境变量
  source ~/catkin_ws/devel/setup.bash
  ```
  

##### 2.2.3 SLAM建图


- 配置参数解析与修改

  在线建图与离线建图修改

  `/LeGO-LOAM/LeGO-LOAM/launch/run.launch`

  ```html
  <launch>
      
      <!--- Sim Time -->
      <param name="/use_sim_time" value="true" />
      # 在线建图为false，离线建图为true
  ```

  rostopic名称修改：针对具体使用IMU话题名称

  激光雷达参数修改：针对速腾聚创RoboSense RS-Helios 32线激光雷达

  `/LeGO-LOAM/LeGO-LOAM/include/utility.h`

  ```cpp
  // 程序订阅的rostopic名称
  extern const string pointCloudTopic = "/velodyne_points";
  // extern const string imuTopic = "/imu/data";
  extern const string imuTopic = "/mavros/imu/data"; // 使用无人机飞控内置IMU
  
  // RS-Helios
  extern const int N_SCAN = 32; // 激光雷达线数
  extern const int Horizon_SCAN = 1800; // 每条线发射水平点数
  extern const float ang_res_x = 0.2; // 水平分辨率
  extern const float ang_res_y = 1.5; // 垂直分辨率
  extern const float ang_bottom = 55; //最下方激光与水平面夹角
  extern const int groundScanInd = 10; // 地面线数，不确定依据是什么
  ```

  重新编译

  ```shell
  cd ~/catkin_ws
  catkin_make -j1
  ```

  https://zhuanlan.zhihu.com/p/386449627

  https://blog.csdn.net/weixin_44208916/article/details/106094490

- 运行算法

  ```shell
  roslaunch lego_loam run.launch
  ```

- 运行rosbag

  ```shell
  # rosbag中激光雷达点云数据与IMU数据话题名称一致，否则需要更改
  rosbag play *.bag --clock --topic /velodyne_points /imu/data
  ```


- 点云地图保存与查看

  接近建图结束时执行record，录制建出地图的话题

  ```shell
  rosbag record -o out /laser_cloud_surround
  ```

  另存为pcd格式文件

  ```shell
  rosrun pcl_ros bag_to_pcd input.bag /laser_cloud_surround pcd
  ```

  最后一个pcd文件为最终建图结果，可使用pcl_viewer查看

  ```shell
  pcl_viewer *.pcd
  ```

  https://blog.csdn.net/m0_37931718/article/details/107832573

- 相关参考

  https://blog.csdn.net/weixin_41281151/article/details/113558183

  https://blog.csdn.net/weixin_43863870/article/details/120044876

  https://blog.csdn.net/weixin_43990795/article/details/121536851
  
  https://blog.csdn.net/weixin_44444810/article/details/121659270

#### 2.3 使用移动小车平台进行激光点云数据采集

##### 2.3.1 平台组成

- 移动小车

  使用遥控器或手机蓝牙遥控，手机蓝牙可调节速度。使用专用充电器单独充电。

- 无人机

  使用无人机端Nano板作为上位机，连接无人机飞控板，使用飞控内置IMU采集IMU数据。Nano板与飞控分别独立供电，Nano板使用降压模块连接锂电池。

- 激光雷达

  使用RS-Helios 32线激光雷达采集激光点云数据，使用网线连接Nano，由220v移动电源配合充电器供电。

- 摄像头

  使用USB相机采集视觉数据，连接Nano使用，由Nano供电。

- 无线路由器

  使用无线路由器实现对Nano的远程桌面控制，由220v移动电源配合充电器供电。

- 远程桌面

  可使用手机、笔记本或平板电脑等移动设备通过Nomachine实现远程桌面查看与控制，与Nano同时连接至无线路由器，实现局域网内通信。

##### 2.3.2 采集流程

- 节点运行

  在Nano板运行的ROS中实现通信与数据的采集，已集成脚本`~/shell/middle/rslidar.sh`

  ```shell
  cd ~/shell/middle
  ./rslidar.sh
  ```

  相关节点如下：

  ```shell
  #启动ROS
  roscore
  #激光雷达点云采集
  roslaunch rslidar_sdk start.launch
  #点云格式转换
  rosrun rs_to_velodyne rs_to_velodyne XYZIRT XYZIRT
  # 启动mavros，ros与飞控通信，配置串口参数与远程地面站地址参数
  roslaunch mavros px4.launch fcu_url:="/dev/ttyTHS1:921600" gcs_url:="udp://@192.168.1.100"
  # 启动T265双目相机，VSLAM定位，可不使用
  roslaunch realsense2_camera rs_camera.launch
  #启动USB相机，ROS中获取视觉数据
  roslaunch usb_cam usb_cam-test.launch
  #启动飞控位姿估计，ROS中获取IMU数据
  roslaunch px4_command px4_pos_estimator.launch
  # 启动飞控速度控制，可不使用
  roslaunch px4_command px4_vel_controller.launch
  ```

- 数据采集

  使用rosbag录制rostopic方式记录数据

  ```shell
  rosbag record -o rslidar.bag /velodyne_points /mavros/imu/data /usb_cam/image_raw
  ```

  数据采集结束 `Ctrl+c`必须正常结束终端，否则bag文件损坏无法读取

  相关topic如下：

  `/velodyne_points`——转换格式后的激光雷达点云数据

  `/mavros/imu/data`——飞控IMU数据

  `/usb_cam/image_raw`——USB摄像头数据

  
