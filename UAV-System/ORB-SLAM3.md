# 文档题目

## 双目视觉定位

### 1. OBR-SLAM3 算法双目视觉定位

#### 1.1 ORB-SLAM3 安装

##### 1.1.1 依赖安装

- 安装 Pangolin

  ```shell
  # Clone Pangolin along with it's submodules
  git clone --recursive  https://github.com/stevenlovegrove/Pangolin.git
  cd Pangolin
  ./scripts/install_prerequisites.sh --dry-run recommended
  ./scripts/install_prerequisites.sh -m brew all
  ```

  运行`./scripts/install_prerequisites.sh -m brew all`报错：

  Error: No preferred package managers from list [brew] found. Use -m to select manually.

  解决方法：安装 linuxbrew-wrapper

  ```shell
  sudo apt install linuxbrew-wrapper
  ```

  https://github.com/stevenlovegrove/Pangolin

  https://blog.csdn.net/BigHandsome2020/article/details/123435000