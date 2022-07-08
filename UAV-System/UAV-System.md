---
typora-root-url: ..\picture
---

#  实验室无人机平台及相关应用

## 无人机平台

<img src="https://cnmafia.oss-cn-beijing.aliyuncs.com/Typora_img/202206222235929.png" alt="结构图 - 副本" style="zoom: 20%;" />

### 1. 硬件

<img src="https://cnmafia.oss-cn-beijing.aliyuncs.com/Typora_img/202206230000540.png" alt="platform2" style="zoom:50%;" />

<img src="https://cnmafia.oss-cn-beijing.aliyuncs.com/Typora_img/202206230000499.png" alt="platform1" style="zoom:50%;" />

#### 1.1 无人机本体
<img src="https://cnmafia.oss-cn-beijing.aliyuncs.com/Typora_img/202206222226984.png" alt="未标题-2" style="zoom: 15%;" />

<img src="https://cnmafia.oss-cn-beijing.aliyuncs.com/Typora_img/202206222228080.png" alt="未标题-8" style="zoom:13%;" />

##### 1.1.1 四旋翼无人机机架

实验室无人机平台为定制碳纤维机架，使用过程中应定期检查螺丝松紧程度，必要时应换用防松螺母。AMOV无人机的长铝柱长度为50mm，实验室有备用材料。

##### 1.1.2 Pixhawk 2.4.8 飞控板

- 接口

  如图所示为飞控板接口，包括正面接口与尾部接口两部分。实验室无人机平台使用了其中部分接口，后续会作说明。具体接口功能与接口线序参考：

  http://playuav.com/article/232

  <img src="https://cnmafia.oss-cn-beijing.aliyuncs.com/Typora_img/202206222240161.png" alt="image-20220622224003856" style="zoom: 33%;" />

  注意，Pixhawk 2.4.8与Pixhawk 4接口不同，连接线端子不同，实验室有部分备份。


   - 供电

     Pixhawk可以通过两种方式进行供电，一是通过侧面的Micro USB接口连接计算机进行供电，此方式通常为连接计算机进行固件刷写与地面站设置时使用；二是通过正面的`POWER`六引脚接口进行供电，此方式通常为无人机飞行时使用。Pixhawk的额定电压为5V，不能使用锂电池为其直接供电，高压电源会击穿飞控电路，需采用合适的降压模块。

     <img src="https://cnmafia.oss-cn-beijing.aliyuncs.com/Typora_img/202206222241323.png" alt="image-20220622224133100" style="zoom:30%;" />

     实验室无人机平台分别使用降压模块与分电板自带的六引脚输出接口进行降压供电，线序与POWER接口一致，按顺序连接即可正常供电。

     <img src="https://cnmafia.oss-cn-beijing.aliyuncs.com/Typora_img/202206222244312.png" alt="image-20220622224453189" style="zoom: 50%;" />

     同款分电板购买链接：https://m.tb.cn/h.fwrUNkD?tk=ZWXF2o0iPGR


   - 安全开关

     安全开关用于确保操作安全，通过SWITCH接口连接飞控板，无安全开关也可以正常飞行。通常在无人机起飞前要通过长按安全开关来人工确认就绪状态，总共分为3个阶段：

     1. 飞控启动过程中安全开关为快速闪动；
     
     2. 飞控启动完成后安全开关为慢闪；

     3. 手动长按安全开关后开关变为双闪（两次快速闪动）。
     
        通过数据线连接至飞控板`SWITCH`接口。
     
     <img src="https://cnmafia.oss-cn-beijing.aliyuncs.com/Typora_img/202206222247328.png" alt="image-20220622224726178" style="zoom: 25%;" />


- 蜂鸣器

     蜂鸣器用于发出不同的声响以指示不同的飞机状态以及低电量警报，通过BUZZER接口连接飞控板，无蜂鸣器也可以正常飞行。通过数据线连接至飞控板`BUZZER`接口。

     <img src="https://cnmafia.oss-cn-beijing.aliyuncs.com/Typora_img/202206222246163.png" alt="image-20220622224647060" style="zoom: 33%;" />

##### 1.1.3 电调


   - 电调类型

     电调通过来自飞控的PWM信号或其他协议控制信号将锂电池电源分出电机电路并加以控制，根据具体指令实现电机的转动。实验室无人机平台分别使用单电机电调与四合一电调。电调的最高电压为 Li-Po XS（X节锂电池）表示，4S电池要求电调所支持的最高电压为16.8V以上。最大电流通常是10A、15A、20A、30A或40A。如果在无人机飞行过程中需要电机高速转动，就需要更高的电压和更大的电流，通常在450mm轴距以下的多旋翼中采用20A以下的电调即可满足需要。

     <img src="https://cnmafia.oss-cn-beijing.aliyuncs.com/Typora_img/202206222302605.png" alt="image-20220622230255399" style="zoom: 33%;" />

     同款电调购买链接：https://m.tb.cn/h.fFutN5F?tk=6YWi2o0Nakr


   - 安装与连接

     无人机工作时，电调的电压与电流均较大，因此存在安全隐患，需要使用绝缘胶套等对电调板和接线处进行包裹；同时电调板应固定于无人机机架中心位置，与其他部件形成隔离，尤其是遥控接收机，防止造成信号干扰。

     电调通过四根信号线连接至飞控板尾部接口，四根线分别对应四个电机的控制，需按照正确顺序进行连接，接口及线序如图所示。

     <img src="https://cnmafia.oss-cn-beijing.aliyuncs.com/Typora_img/202206222304978.jpg" alt="R" style="zoom: 67%;" />

##### 1.1.4 分电板

   -  选型与安装

     分电板将锂电池电源进行分电，分出不同电压的电源用于无人机不同部件的供电。去电池电路(Battery Eliminator Circuit,BEC) 是一种用来取代电池提供电力的电路，实验室无人机平台所用分电板自带BEC，为机载电脑提供5V3A稳定供电。

     考虑到安全问题，分电板也应固定在机架中心内部位置，与其他部件形成隔离，可以使用绝缘热缩胶套包裹。
     
     <img src="https://cnmafia.oss-cn-beijing.aliyuncs.com/Typora_img/202206222312541.jpg" alt="81fa2bc314c5ced65d40f78bf176dfb" style="zoom: 33%;" />

##### 1.1.5 锂电池

   - 电池要求

     实验室无人机平台使用4S锂电池，3S也可以使用，需要在QGC中设置电源。使用哔哔响（电压测量模块）测量电池电压，保持电压不低于15V使用。使用锂电池充电器充电，充电电流设置为2A，充电至电压为16.7~16.8V时电池充满。

##### 1.1.6 电机

   - 安装与连接

     无人机使用无刷直流电机，每个电机通过两条供电线与一条信号线连接至电调，电机分为正反转两个方向，螺旋桨安装也分正反转方向，通过电机和螺旋桨的黑白色标志区分。

##### 1.1.7 遥控模块

   - 安装与连接

     遥控模块包括遥控器与接收机，遥控器需保证电量充足，开机时需拨杆全部朝上，摇杆全部朝下。接收机固定于无人机上，通过天线接收遥控器信号。通过供电线与信号线连接至飞控板实现供电与信号通信，还可以连接电压回传模块，将无人机锂电池电压回传至遥控器显示。

     <img src="https://cnmafia.oss-cn-beijing.aliyuncs.com/Typora_img/202206222310481.jpg" alt="b1f051bf0f2f196ecb3238a846d2928" style="zoom:33%;" />
     
     <img src="https://cnmafia.oss-cn-beijing.aliyuncs.com/Typora_img/202206222310170.jpg" alt="b2e233cefe7b8a783ad9dea2c612575" style="zoom:33%;" />
     
     <img src="https://cnmafia.oss-cn-beijing.aliyuncs.com/Typora_img/202206222312017.jpg" alt="2b68b11933a4f41eaad88a150bd419a" style="zoom:33%;" />
     
     供电线与信号线需正确连接至飞控板尾部接口，注意正负极不能接反。

#### 1.2 机载电脑与传感器

##### 1.2.2 激光雷达

- 选型与安装

  型号：RPLIDAR A2 激光雷达

  通过数据线与转接板连接至Nano的USB接口，波特率默认设置为115200。

  ![image-20220622231437024](https://cnmafia.oss-cn-beijing.aliyuncs.com/Typora_img/202206222314016.png)

##### 1.2.3 双目相机

- 选型与安装

  型号：Intel Realsense T265 双目相机

  通过数据线连接至Nano的USB接口，数据线为特殊接口。

  <img src="https://cnmafia.oss-cn-beijing.aliyuncs.com/Typora_img/202206222315952.png" alt="image-20220622231528866" style="zoom: 50%;" />

##### 1.2.4  激光定高

- 选型与安装

  型号：TFmini Plus 激光定高雷达

  通过四根数据线连接至飞控板的 `TELEM 1`、`TELEM 2` 或 `SERIAL 4/5` 接口，通过QGC进行参数设置，目前使用`TELEM1`。

  <img src="https://cnmafia.oss-cn-beijing.aliyuncs.com/Typora_img/202206222316448.png" alt="image-20220622231628351" style="zoom: 33%;" />

##### 1.2.5 GPS

- 安装与连接

  通过数据线连接至飞控板的`GPS`接口。

  <img src="https://cnmafia.oss-cn-beijing.aliyuncs.com/Typora_img/202206222318819.jpg" alt="28f472e8a812d1bee59052f509bce6b" style="zoom:33%;" />

##### 1.2.6 光流计

- 选型与安装

  型号：PX4FLOW 光流计

  通过数据线连接至飞控板的`I2C`接口。

  安装方向可按照默认方向进行安装，如果安装方向与默认方向不同，应在 QGC 内进行参数修改。

  <img src="https://docs.px4.io/master/assets/img/px4flowalignwithpixhawk.72ce1e4f.jpg" alt="PX4Flow align with Pixhawk" style="zoom: 80%;" />

  https://docs.px4.io/master/zh/sensor/px4flow.html

- 镜头对焦

  使用 USB 数据线连接光流计与 PC，可在 QGC 中观察光流计的成像，通过观察一定距离处成像画面的清晰程度进行对焦，官方建议3m以上，室内定位可设置为1.5m左右。

  https://blog.csdn.net/liberatetheus/article/details/77914246

##### 1.2.7 单目相机

- 选型与安装

  型号：普通USB摄像头

  通过数据线连接至Nano的USB接口。

### 2. 软件

#### 2.1 Pixhawk 飞控板 PX4 1.9.2 飞控固件

PX4由苏黎世联邦理工学院的计算机视觉与几何实验室的一个软硬件项目 PIXHAWK 演变而来，目的在于为学术、爱好和工业团体提供一款低成本高性能的高端的自动驾驶仪，它由来自工业界和学术界的世界级开发人员开发，并得到活跃的全球社区的支持，为各种载具提供支持。

- 版本选择

  实验室无人机平台稳定运行的 PX4 版本为 V1.9.2。
  
  对于同一版本的 PX4 固件，由于主控芯片的差异，编译时还应根据不同版本的飞控板进行选择，Pixhawk 2.4.8 飞控板对应的飞控固件为 `px4_fmu-v3_default.px4`，Pixhawk 4 飞控板对应的飞控固件为 `px4_fmu-v5_default.px4`。

##### 2.1.1 固件编译

- 固件下载与编译

  PX4官方Github项目地址：https://github.com/PX4/PX4-Autopilot

  ```shell
  # 默认已有 ROS 工作空间，如 /catkin_ws
  cd ~/catkin_ws/src
  git clone -b v1.9.2 https://github.com/PX4/Firmware.git 
  cd Firmware
  # 加载子模块
  git submodule update --init --recursive
  # Pixhawk 2.4.8
  make px4fmu-v3_default
  # Pixhawk 4
  make px4fmu-v5_default
  ```
  编译生成的固件文件存放于 `Firmware/build/`。

##### 2.1.2 固件刷写

- 固件刷写

  先打开 QGC 地面站（不连接无人机），点击“固件”，无人机连接电脑，QGC 自动检测出飞控芯片版本以及现有固件版本，根据提示选择自定义固件，选择文件位置加载编译生成的固件文件，等待刷写完成即可。

- 注意事项

  1. 固件刷写前如有需要应备份飞控参数，固件刷写后参数可能丢失；
  2. 飞控多次无法正常启动时，可选择重新刷写固件。

##### 2.1.4 机架选择

- 机架选择

  可选择`DJI F450`或`DJI F330`，选择其他机架可能无法正常控制电机，且默认PID参数不同。
  
  ![image-20220703160006794](https://cnmafia.oss-cn-beijing.aliyuncs.com/Typora_img/202207031600971.png)

##### 2.1.3 参数配置

- 参数配置

  通过QGC“参数”进行参数的修改与配置。
  
  ```shell
  # 设置无人机飞行位置来源，根据使用传感器选择，使用双目相机时，设置为24，即vision position fusion 和 vision yaw fusion。
  EKF2_AID_MASK = 24 
  # 设置无人机飞行高度数据来源，使用激光定高，设置为距离传感器
  EKF2_HGT_MODE = Range sensor
  # 启用距离传感器
  EKF2_RNG_AID = Range aid enabled
  # 设置飞控收发MAVLINK消息通信的接口
  MAV_1_CONFIG = TELEM2
  # 设置激光定高与飞控数据通信的接口
  SEN_TFMINI_CFG = TELEM1
  # 禁用安全开关（可选）
  CBRK_IO_SAFETY = 22027
  # 禁用电源模块检查（可选）
  CBRK_SUPPLY_CHK = 894281
  # 禁用USB连接解锁飞机检查（可选）
  CBRK_USB_CHK = 197848
  ```
  
  ![image-20220622232554827](https://cnmafia.oss-cn-beijing.aliyuncs.com/Typora_img/202206222325878.png)
  
  ![image-20220622232632051](https://cnmafia.oss-cn-beijing.aliyuncs.com/Typora_img/202206222326095.png)
  
  ![image-20220622232640320](https://cnmafia.oss-cn-beijing.aliyuncs.com/Typora_img/202206222326356.png)
  
  ![image-20220622232649533](https://cnmafia.oss-cn-beijing.aliyuncs.com/Typora_img/202206222326575.png)

- 注意事项
  1. 完成新的修改后可通过导出参数文件进行备份。
  2. 某些参数修改后需重启飞控才能生效。

##### 2.1.4 传感器校准

- 校准流程

  根据QGC传感器校准流程，分步进行校准，尽量保持校准动作与姿态的标准。

##### 2.1.5 遥控器校准

- 校准流程

  根据QGC传感器校准流程，分步进行校准。

##### 2.1.6 飞行模式

- 模式介绍

  ![img](https://cnmafia.oss-cn-beijing.aliyuncs.com/Typora_img/202206222342289.png)

- 设置流程

  ![image-20220703160117459](https://cnmafia.oss-cn-beijing.aliyuncs.com/Typora_img/202207031601573.png)

- 遥控器对应状态

  原始开机状态，所有拨杆均在最上位置，此时为`Stabilized`模式。
  
  <img src="https://cnmafia.oss-cn-beijing.aliyuncs.com/Typora_img/202207042152743.png" alt="image-20220704215200998" style="zoom: 17.3%;" />
  
  右一拨杆在最下位置，其余不变，此时为`Offboard`模式。
  
  <img src="https://cnmafia.oss-cn-beijing.aliyuncs.com/Typora_img/202207042153455.jpg" alt="2022-07-03 193619" style="zoom:20%;" />
  
  右二拨杆在中间位置，其余不变，此时为`Position`模式。
  
  <img src="https://cnmafia.oss-cn-beijing.aliyuncs.com/Typora_img/202207042221126.jpg" alt="2022-07-03 193626" style="zoom:20%;" />

#### 2.2 Jetson Nano 机载电脑 Ubuntu 18.04

##### 2.2.1 系统备份与烧录

- 备份镜像

  将Nano系统内存卡使用读卡器连接电脑（ubuntu系统），查看内存设备与地址，根据内存卡容量确定地址。

  ```shell
  sudo fdisk -u -l
  ```

  确定内存卡地址后生成镜像 

  ```shell
  # if为内存卡地址，of为生成镜像存放的位置
  sudo dd bs=8M if=/dev/sdc* of=(location)/nano.img
  ```

  查看进度

  ```shell
  sudo watch -n 1 pkill -USR1 -x dd
  ```

- 烧录镜像

  将一张空白内存卡（容量大于镜像大小）通过读卡器连接电脑，查看内存设备与地址，根据内存卡容量确定地址。

    ```shell
  sudo fdisk -u -l
    ```
  
  确定内存卡地址后烧录镜像
  
  ```shell
  # 在镜像存放位置下终端执行指令，of为烧录内存卡地址
  sudo dd of=/dev/sdc  bs=120M status=progress
  ```
  
  查看进度
  
  ```shell
  sudo watch -n 1 pkill -USR1 -x dd
  ```
  

##### 2.2.2 远程控制

- 远程桌面软件

  使用 Nomachine 或 ToDesk 作为远程桌面查看与控制软件，局域网内使用 Nomachine，互联网使用ToDesk。ToDesk使用较为方便，延迟较低。Nano供电电压不足时，USB接口供电不足，无线网卡可能不工作，或者有时开机后不会自动连接WIFI，需要用HDMI线连接查看。

- 远程控制流程

  Nomachine：将无人机端 Nano 与地面端电脑连接同一 WIFI，即同一无线路由器，保证在同一局域网内，在无人机端与地面端电脑分别运行 Nomachine，在地面端 Nomachine 输入无人机端 Nano 网络地址。

  <img src="https://cnmafia.oss-cn-beijing.aliyuncs.com/Typora_img/202206291544057.png" alt="image-20220629154400924" style="zoom:67%;" />
  
  查看 Nano 无线网卡IP地址
  
  ```shell
  ifconfig
  ```
  
  其中`wlan0`为无线网卡，其他为有线网卡等。
  
  <img src="https://cnmafia.oss-cn-beijing.aliyuncs.com/Typora_img/202207031553792.png" alt="Screenshot from 2022-07-01 20-18-28" style="zoom: 80%;" />
  
  ToDesk：将无人机端 Nano 与地面端电脑连接网络，无需保证同一局域网内，在无人机端与地面端电脑分别运行 ToDesk，在地面端输入无人机端 ToDesk 设备代码和密码，建立连接。
  
  <img src="https://cnmafia.oss-cn-beijing.aliyuncs.com/Typora_img/202207051226637.png" alt="image-20220705122620521" style="zoom:50%;" />
