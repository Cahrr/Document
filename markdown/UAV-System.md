#  实验室无人机平台及相关应用

## 无人机平台

### 1. 硬件

#### 1.1 无人机本体

##### 1.1.1 四旋翼无人机机架

实验室无人机平台为定制碳纤维机架，使用过程中应定期检查螺丝松紧程度，必要时应换用防松螺母。AMOV无人机的长铝柱长度为50mm，实验室有备用材料。

图片

##### 1.1.2 Pixhawk 2.4.8 飞控板

- 接口

  如图所示为飞控板接口，包括正面接口与尾部接口两部分。实验室无人机平台使用了其中部分接口，后续会作说明。具体接口功能与接口线序参考：

  http://playuav.com/article/232

  图片

  注意，Pixhawk 2.4.8与Pixhawk 4接口不同，连接线端子不同，实验室有部分备份。


   - 供电

     Pixhawk可以通过两种方式进行供电，一是通过侧面的Micro USB接口连接计算机进行供电，此方式通常为连接计算机进行固件刷写与地面站设置时使用；二是通过正面的“POWER”六引脚接口进行供电，此方式通常为无人机飞行时使用。Pixhawk的额定电压为5V，不能使用锂电池为其直接供电，高压电源会击穿飞控电路，需采用合适的降压模块。

     图片

     实验室无人机平台分别使用降压模块与分电板自带的六引脚输出接口进行降压供电，线序与POWER接口一致，按顺序连接即可正常供电。

     图片

     同款分电板购买链接：


   - 安全开关

     安全开关用于确保操作安全，通过SWITCH接口连接飞控板，无安全开关也可以正常飞行。通常在无人机起飞前要通过长按安全开关来人工确认就绪状态，总共分为3个阶段：

     1. 飞控启动过程中安全开关为快速闪动；
     2. 飞控启动完成后安全开关为慢闪；
     3. 手动长按安全开关后开关变为双闪（两次快速闪动）。

     图片


- 蜂鸣器

     蜂鸣器用于发出不同的声响以指示不同的飞机状态以及低电量警报，通过BUZZER接口连接飞控板，无蜂鸣器也可以正常飞行。

     图片

##### 1.1.3 电调


   - 电调类型

     电调通过来自飞控的PWM信号或其他协议控制信号将锂电池电源分出电机电路并加以控制，根据具体指令实现电机的转动。以实验室无人机平台分别使用单电机电调与四合一电调。电调的最高电压为Li-Po XS（X节锂电池）表示，4S电池要求电调所支持的最高电压为16.8V以上。最大电流通常是10A、15A、20A、30A或40A。如果在无人机飞行过程中需要电机高速转动，就需要更高的电压和更大的电流，通常在450mm轴距以下的多旋翼中采用20A以下的电调即可满足需要。

     图片

     同款电调购买链接：


   - 安装与连接

     无人机工作时，电调的电压与电流均较大，因此存在安全隐患，需要使用绝缘胶套等对电调板和接线处进行包裹；同时电调板应固定于无人机机架中心位置，与其他部件形成隔离，尤其是遥控接收机，防止造成信号干扰。

     电调通过四根信号线连接至飞控板尾部接口，四根线分别对应四个电机的控制，需按照正确顺序进行连接，接口及线序如图所示。

     图片

##### 1.1.4 分电板

   -  选型与连接

     分电板将锂电池电源进行分电，分出不同电压的电源用于无人机不同部件的供电。去电池电路(Battery Eliminator Circuit,BEC) 是一种用来取代电池提供电力的电路，实验室无人机平台所用分电板自带BEC，为机载电脑提供5V3A稳定供电。

     考虑到安全问题，分电板也应固定在机架中心内部位置，与其他部件形成隔离，可以使用绝缘热缩胶套包裹。

##### 1.1.5 锂电池

   - 电池要求

     实验室无人机平台使用4S锂电池，3S也可以使用，需要在QGC中设置电源。使用哔哔响（电压测量模块）测量电池电压，保持电压不低于15V使用。使用锂电池充电器充电，充电电流设置为2A，充电至电压为16.7~16.8V时电池充满。

##### 1.1.6 电机

   - 安装与连接

     无人机使用无刷直流电机，每个电机通过两条供电线与一条信号线连接至电调，电机分为正反转两个方向，螺旋桨安装也分正反转方向，通过电机和螺旋桨的黑白色标志区分。

##### 1.1.7 遥控模块

   - 安装与连接

     遥控模块包括遥控器与接收机，遥控器需保证电量充足，开机时需拨杆全部朝上，摇杆全部朝下。接收机固定于无人机上，通过天线接收遥控器信号。通过供电线与信号线连接至飞控板实现供电与信号通信，还可以连接电压回传模块，将无人机锂电池电压回传至遥控器显示。

     供电线与信号线需正确连接至飞控板尾部接口，注意正负极不能接反。

#### 1.2 机载电脑与传感器

##### 1.2.1 激光雷达

- 选型与安装

  图片

  型号：RPLIDAR A2 激光雷达

  通过数据线与转接板连接至Nano的USB接口，波特率默认设置为115200。

##### 1.2.2 双目相机

- 选型与安装

  图片

  型号：Intel Realsense T265 双目相机

  通过数据线连接至Nano的USB接口，数据线为特殊接口。

##### 1.2.3  激光定高

- 选型与安装

  图片

  型号：TFmini Plus 激光定高雷达

  通过四根数据线连接至飞控板的 TELEM 1、TELEM 2 或 SERIAL 4/5 接口，通过QGC进行参数设置。


##### 1.2.4 GPS

- 安装与连接5

  图片

  通过数据线连接至飞控板的 GPS 接口。

### 2. 软件

#### 2.1 Pixhawk 飞控板 PX4 1.9.2 飞控固件

PX4由苏黎世联邦理工学院的计算机视觉与几何实验室的一个软硬件项目 PIXHAWK 演变而来，目的在于为学术、爱好和工业团体提供一款低成本高性能的高端的自动驾驶仪，它由来自工业界和学术界的世界级开发人员开发，并得到活跃的全球社区的支持，为各种载具提供支持。

- 版本选择

  实验室无人机平台稳定运行的 PX4 版本为 V1.9.2。对于同一版本的 PX4 固件，由于主控芯片的差异，编译时还应根据不同版本的飞控板进行选择，Pixhawk 2.4.8 飞控板对应的飞控固件为 px4_fmu-v3_default.px4，Pixhawk 4 飞控板对应的飞控固件为 px4_fmu-v5_default.px4。

##### 2.1.1 固件编译

- 固件下载与编译

  PX4官方Github项目地址：[PX4/PX4-Autopilot: PX4 Autopilot Software (github.com)](https://github.com/PX4/PX4-Autopilot)

  ```shell
  #默认已有 ROS 工作空间，如 /catkin_ws
  cd ~/catkin_ws/src
  git clone -b v1.9.2 https://github.com/PX4/Firmware.git 
  cd Firmware
  #
  git submodule update --init --recursive
  # Pixhawk 2.4.8
  make px4fmu-v3_default
  # Pixhawk 4
  make px4fmu-v5_default
  ```
  编译生成的固件文件存放于 Firmware/build/，

##### 2.1.2 固件刷写

- 固件刷写

  先打开 QGC 地面站（不连接无人机），点击“固件”，无人机连接电脑，QGC 自动检测出飞控芯片版本以及现有固件版本，根据提示选择自定义固件，选择文件位置加载编译生成的固件文件，等待刷写完成即可。

- 注意事项

  1. 固件刷写前如有需要应备份飞控参数，固件刷写后参数可能丢失。
  2. 飞控多次无法正常启动时，可选择重新刷写固件。
  3. 


##### 2.1.3 参数配置

- 参数设置：

  EKF2_AID_MASK = 24 
  EKF2_HGT_MODE = Range sensor
  EKF2_RNG_AID = Range aid enabled
  MAV_1_CONFIG = TELEM2
  SEN_TFMINI_CFG = TELEM1
  CBRK_IO_SAFETY = 22027
  CBRK_SUPPLY_CHK = 894281
  CBRK_USB_CHK = 197848

##### 2.1.4 飞机校准

#### 2.2 Jetson Nano 机载电脑 Ubuntu 18.04

##### 2.2.1 镜像拷贝

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

- 运行算法

  ```shell
  roslaunch lego_loam run.launch
  ```

- 运行rosbag

  ```shell
  # rosbag中激光雷达点云数据与IMU数据话题名称一致，否则需要更改
  rosbag play *.bag --clock --topic /velodyne_points /imu/data
  ```

- 配置参数解析与修改

  在线建图与离线建图修改

  /LeGO-LOAM/LeGO-LOAM/launch/run.launch

  ```html
  <launch>
      
      <!--- Sim Time -->
      <param name="/use_sim_time" value="true" />
      # 在线建图为false，离线建图为true
  ```

  rostopic名称修改：针对具体使用IMU话题名称

  激光雷达参数修改：针对速腾聚创RoboSense RS-Helios 32线激光雷达

  /LeGO-LOAM/LeGO-LOAM/include/utility.h

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
  extern const int groundScanInd = 10; // 地面线数
  ```

  https://zhuanlan.zhihu.com/p/386449627

  https://blog.csdn.net/weixin_44208916/article/details/106094490

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
  pcl_viewer xx.pcd
  ```

  https://blog.csdn.net/m0_37931718/article/details/107832573

- 相关参考

  https://blog.csdn.net/weixin_41281151/article/details/113558183

