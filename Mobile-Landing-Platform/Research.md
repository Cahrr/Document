#  无人机视觉引导移动降落平台

### 1. 降落流程

#### 1.1 无人机室内定位

1.1.1 双目视觉定位

- T265 双目相机
- VSLAM

#### 1.2 无人机定点飞行

#### 1.3 Aruco码识别

#### 1.4 地面平台接管

#### 1.5 无人机移动跟随

#### 1.6 无人机自主降落

### 2. 控制策略

#### 2.1 单环位置式PID

##### 2.1.1 程序代码

```cpp
// 时间微分
c_time = ros::Time::now();
current_time = c_time.toSec();
dt = current_time - last_time;

if (dt > 0.001)
{
    // 误差e
    err_x = 0.0 - Xbody;
    err_y = 0.0 - Ybody;
    err_z = H - Zbody;

    err_sum_x += err_x;
    err_sum_y += err_y;
    err_sum_z += err_z;

    if (err_sum_y > 2)
    {
        err_sum_y = 2;
    }
    if (err_sum_y < -2)
    {
        err_sum_y = -2;
    }

    if (err_sum_x > 2)
    {
        err_sum_x = 2;
    }
    if (err_sum_x < -2)
    {
        err_sum_x = -2;
    }

    if (err_sum_z > 2)
    {
        err_sum_z = 2;
    }
    if (err_sum_z < -2)
    {
        err_sum_z = -2;
    }

    // 误差的微分ec
    derr_x = (err_x - last_err_x) / dt;
    derr_y = (err_y - last_err_y) / dt;
    derr_z = (err_z - last_err_z) / dt;
    detector_uav_vel_cmd.vel.linear.x = x_kp * err_x + x_ki * err_sum_x + x_kd * derr_x;
    detector_uav_vel_cmd.vel.linear.y = y_kp * err_y + y_ki * err_sum_y + y_kd * derr_y;
    detector_uav_vel_cmd.vel.linear.z = z_kp * err_z + z_ki * err_sum_z + z_kd * derr_z;
    detector_vel_pub.publish(detector_uav_vel_cmd);
}
last_err_x = err_x;
last_err_y = err_y;
last_err_z = err_z;
last_time = current_time;
```

##### 2.1.2 参数调整

- P 调节

  | P 数值 | 实验效果          |
  | ------ | ----------------- |
  | 0.4    | 响应稍慢          |
  | 0.6    | 振荡较大          |
  | 0.4    | 振荡较小 收敛较慢 |

- PD 调节

  | P 数值 | D 数值 | 实验效果          |
  | ------ | ------ | ----------------- |
  | 0.4    | 0.04   |                   |
  | 0.4    | 0.1    | 调节频繁 无法收敛 |
  | 0.4    | 0.06   |                   |
  | 0.4    | 0.04   |                   |
  | 0.4    | 0.045  | 效果较好          |
  | 0.4    | 0.05   | 效果较好          |
  | 0.35   | 0.05   |                   |
  | 0.3    | 0.05   |                   |
  | 0.3    | 0.2    |                   |
  | 1      | 0.2    |                   |
  | 0.3    | 0.05   |                   |
  | 0.3    | 0.06   | 效果较好          |
  | 0.3    | 0.065  |                   |
  | 0.3    | 0.08   |                   |

- PID 调节

  | P 数值 | D 数值 | I 数值 | 实验效果          |
  | ------ | ------ | ------ | ----------------- |
  | 0.3    | 0.08   | 0.002  | 有震荡            |
  | 0.3    | 0.08   | 0.001  |                   |
  | 0.27   | 0.08   | 0.001  |                   |
  | 0.32   | 0.065  | 0.001  |                   |
  | 0.32   | 0.03   | 0.001  | 震荡减小 跟随变差 |
  | 0.36   | 0.03   | 0.001  |                   |
  | 0.36   | 0.04   | 0.001  | 震荡较小 跟随较好 |

- 输出速度系数调节 k_vel_x, k_vel_y, k_vel_z

  | k_vel_x | k_vel_y | k_vel_z | 实验效果                                                     |
  | ------- | ------- | ------- | ------------------------------------------------------------ |
  | 1.2     | 1.2     | 1.0     | y轴位置保持-0.1-0.16m误差，y轴速度保持0.05m/s左右，速度指令与指令曲线不一致，但位置几乎不动；x轴误差较小，表现良好；z轴设定高度0.45m，实际保持为0.65m，z轴速度保持为0.12m/s左右。原因：未设置flag。 |
  | 1.2     | 1.2     | 1.0     | 修改flag指令规则，取消定点飞行后的hold等待状态，延长定点飞行等待时间，利用flag的变化中断捕获接管飞机。 |
  | 1.8     | 1.8     | 1.0     |                                                              |
  |         |         |         |                                                              |

#### 2.2 单环增量式PID

##### 2.2.1 程序代码

```cpp
x_icm_pid.init(0.0, x_kp, x_ki, x_kd);
y_icm_pid.init(0.0, y_kp, y_ki, y_kd);
z_icm_pid.init(H, z_kp, z_ki, z_kd);

x_icm_vel = x_icm_pid.Incremental_PID_output(Xbody);
y_icm_vel = y_icm_pid.Incremental_PID_output(Ybody);
z_icm_vel = z_icm_pid.Incremental_PID_output(Zbody);

detector_uav_vel_cmd.vel.linear.y += y_icm_vel;
detector_uav_vel_cmd.vel.linear.x += x_icm_vel;
detector_uav_vel_cmd.vel.linear.z += z_icm_vel;

detector_vel_pub.publish(detector_uav_vel_cmd);
ROS_INFO("x_vel:%f", detector_uav_vel_cmd.vel.linear.x);
ROS_INFO("x_icm_vel:%f", x_icm_vel);
ROS_INFO("y_vel:%f", detector_uav_vel_cmd.vel.linear.y);
ROS_INFO("y_icm_vel:%f", y_icm_vel);
ROS_INFO("z_vel:%f", detector_uav_vel_cmd.vel.linear.z);
ROS_INFO("z_icm_vel:%f", z_icm_vel);
```

##### 2.2.2 参数调整

使用 P=0.36，I=0.001, D=0.05 作为初始参数，控制效果较好。

#### 2.3 模糊PID

##### 2.3.1 程序代码

```cpp
c_time = ros::Time::now();
current_time = c_time.toSec();
dt = current_time - last_time;

if (dt > 0.0001)
{

    // 误差e(输入1)
    err_x = 0.0 - Xbody;
    err_y = 0.0 - Ybody;
    err_z = H - Zbody;

    // 误差的微分ec(输入2)
    derr_x = (err_x - last_err_x) / dt;
    derr_y = (err_y - last_err_y) / dt;
    derr_z = (err_z - last_err_z) / dt;

    // 赋PID初值
    if (fuzzy_flag == 0)
    {

        ROS_INFO("fuzzy_flag changed!");
        x_fuzzy_pid.init(0.0, x_kp, x_ki, x_kd);
        y_fuzzy_pid.init(0.0, y_kp, y_ki, y_kd);
        z_fuzzy_pid.init(H, z_kp, z_ki, z_kd);
        fuzzy_flag = 1;
    }

    // delta(P I D)和两个输入的量化因子(需要调整)
    x_scale_kp = 0.036 / 3, x_scale_ki = 0.0002 / 3, x_scale_kd = 0.005 / 3, x_scale_err = 15, x_scale_derr = 15 * dt;
    y_scale_kp = 0.036 / 3, y_scale_ki = 0.0002 / 3, y_scale_kd = 0.005 / 3, y_scale_err = 15, y_scale_derr = 15 * dt;
    z_scale_kp = 0.05 / 3, z_scale_ki = 0.01 / 3, z_scale_kd = 0.01 / 3, z_scale_err = 10, z_scale_derr = 10 * dt;

    // x_fpid_output = x_fuzzy_pid.Fuzzy_PID_output(Xbody, err_x, derr_x, x_scale_kp, x_scale_ki, x_scale_kd, x_scale_err, x_scale_derr);
    // y_fpid_output = y_fuzzy_pid.Fuzzy_PID_output(Ybody, err_y, derr_y, y_scale_kp, y_scale_ki, y_scale_kd, y_scale_err, y_scale_derr, y_fuzzy_icm_pid);
    // z_fpid_output = z_fuzzy_pid.Fuzzy_PID_output(Zbody, err_z, derr_z, z_scale_kp, z_scale_ki, z_scale_kd, z_scale_err, z_scale_derr);

    x_fzy_fpid = x_fuzzy_pid.Fuzzy_PID_output(Xbody, err_x, derr_x, x_scale_kp, x_scale_ki, x_scale_kd, x_scale_err, x_scale_derr, x_fuzzy_icm_pid);        
    y_fzy_fpid = y_fuzzy_pid.Fuzzy_PID_output(Ybody, err_y, derr_y, y_scale_kp, y_scale_ki, y_scale_kd, y_scale_err, y_scale_derr, y_fuzzy_icm_pid);
    z_fzy_fpid = z_fuzzy_pid.Fuzzy_PID_output(Zbody, err_z, derr_z, z_scale_kp, z_scale_ki, z_scale_kd, z_scale_err, z_scale_derr, z_fuzzy_icm_pid);

    x_fzy_pid.init(0.0, x_fzy_fpid[0], x_fzy_fpid[1], x_fzy_fpid[2]);
    y_fzy_pid.init(0.0, y_fzy_fpid[0], y_fzy_fpid[1], y_fzy_fpid[2]);
    z_fzy_pid.init(H, z_fzy_fpid[0], z_fzy_fpid[1], z_fzy_fpid[2]);

    x_fpid_output = pid.Incremental_PID_output(Xbody);
    y_fpid_output = pid.Incremental_PID_output(Ybody);
    z_fpid_output = pid.Incremental_PID_output(Zbody);

    //输出的量化因子(需要调整)
    x_scale_output = 1.0;
    y_scale_output = 1.0;
    z_scale_output = 1.0;

    detector_uav_vel_cmd.vel.linear.x += x_fpid_output * x_scale_output;
    detector_uav_vel_cmd.vel.linear.y += y_fpid_output * y_scale_output;
    detector_uav_vel_cmd.vel.linear.z += z_fpid_output * z_scale_output;

    /*
                if(detector_uav_vel_cmd.vel.linear.y > 0.20)
                {
                    detector_uav_vel_cmd.vel.linear.y = 0.20;
                }

                if(detector_uav_vel_cmd.vel.linear.y < -0.20)
                {
                    detector_uav_vel_cmd.vel.linear.y = -0.20;
                }
                */

    detector_vel_pub.publish(detector_uav_vel_cmd);
}

last_time = current_time;
last_err_x = err_x;
last_err_y = err_y;
last_err_z = err_z;
```

##### 2.3.2 参数调整

使用 P=0.36，I=0.001, D=0.05 作为初始参数，控制效果较好。



#### 2.4 MPC 模型预测控制

##### 2.4.1 程序代码

##### 2.4.2 参数调整



## 3. 飞行实验

#### 3.1 稳定定点实验

#### 3.2 稳定跟随实验

| 实验 | 控制策略  | PID参数                          | 速度系数        | 跟随速度 | 效果        |
| ---- | --------- | -------------------------------- | --------------- | -------- | ----------- |
| 1    | 位置式PID | 0.36, 0.001, 0.04                | 5.0, 5.0, 1.0   | 0.05 m/s | 误差较大    |
| 2    | 位置式PID | 0.36, 0.001, 0.04                | 5.0, 5.0, 1.0   | 0.07 m/s | 误差较大    |
| 3    | 位置式PID | 0.36, 0.001, 0.04                | 10.0, 10.0, 2.0 | 0.07 m/s | 振荡较大    |
| 4    | 位置式PID | 0.36, 0.001, 0.04                | 7.5, 7.5, 2.0   | 0.07 m/s | 振荡较大    |
| 5    | 位置式PID | 0.36, 0.001, 0.04                | 6.0, 6.0, 1.5   | 0.07 m/s | 效果较好    |
| 6    | 位置式PID | X0.36, 0.002, 0.04               | 6.0, 6.0, 1.5   | 0.07 m/s | X 效果较好  |
| 7    | 位置式PID | X0.36, 0.002, 0.04               | 6.0, 6.0, 1.5   | 0.1 m/s  | 振荡较大    |
| 8    | 位置式PID | X0.36, 0.002, 0.04, Z0.02        | 6.0, 6.0, 1.2   | 0.1 m/s  | 滞后较大    |
| 9    | 位置式PID | X0.36, 0.002, 0.04, Y0.08, Z0.02 | 6.0, 6.3, 1.2   | 0.1 m/s  | 滞后改善    |
| 10   | 位置式PID | X0.36, 0.002, 0.04, Y0.1, Z0.02  | 6.0, 6.3, 1.2   | 0.1 m/s  | XY 效果较好 |





| 实验 | 控制策略  | PID参数                                                   | 速度系数      | 跟随速度 | 效果 |
| ---- | --------- | --------------------------------------------------------- | ------------- | -------- | ---- |
| 1    | 位置式PID | X0.36, 0.001, 0.04, Y0.36, 0.002,0.04, Z0.36, 0.001, 0.01 | 6.0, 6.3, 1.1 | 0.05 m/s |      |
|      |           |                                                           |               |          |      |
|      |           |                                                           |               |          |      |
|      |           |                                                           |               |          |      |
|      |           |                                                           |               |          |      |
|      |           |                                                           |               |          |      |
|      |           |                                                           |               |          |      |



学校名称：北京交通大学

税号：1210000040088209X1

地址：北京市海淀区北下关街道上园村3号北京交通大学

电话：010-51685230

开户行：中国工商银行北京新街口支行

账户：0200002909014494801

邮箱：1193213920@qq.com

无线路由器 TP-LINK AC1200 155元

数据线 D435i相机数据连接线 40元

总额 = 195元
