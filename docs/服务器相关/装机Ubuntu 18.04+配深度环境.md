# 装机Ubuntu 18.04+配深度环境

## 装机

戴尔装系统：按F12 选择UEFI中USB Flash Disk，按照安装流程装ubuntu-18.04，装好后点击重启

(或者按Del)

修改启动项：按F2 Boot Sequence中 Boot list option，选择 UEFI，点击apply

进入系统后配网络： ...

安装openssh

sudo apt-get install openssh-server

## 配深度环境

版本不要装太新，显卡和cudnn的版本根据cuda的版本来s

### 换源

进入 /etc/apt

先将sources.list备份到sources.list.bak：sudo cp sources.list sources.list.bak

修改：sudo vi sources.list

将 /etc/apt/sources.list 的内容换成：

```
deb http://mirrors.aliyun.com/ubuntu/ bionic main restricted universe multiverse                                               
deb-src http://mirrors.aliyun.com/ubuntu/ bionic main restricted universe multiverse                                           
                                                                                                                               
deb http://mirrors.aliyun.com/ubuntu/ bionic-security main restricted universe multiverse                                      
deb-src http://mirrors.aliyun.com/ubuntu/ bionic-security main restricted universe multiverse

deb http://mirrors.aliyun.com/ubuntu/ bionic-updates main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic-updates main restricted universe multiverse

deb http://mirrors.aliyun.com/ubuntu/ bionic-proposed main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic-proposed main restricted universe multiverse

deb http://mirrors.aliyun.com/ubuntu/ bionic-backports main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic-backports main restricted universe multiverse
```

更新：

sudo apt-get update

sudo apt-get upgrade

### 显卡驱动

禁用自带的 nouveau nvidia驱动

创建一个文件通过命令 sudo vi /etc/modprobe.d/blacklist-nouveau.conf

并添加如下内容：

blacklist nouveau
options nouveau modeset=0

再更新一下: sudo update-initramfs -u

修改后需要重启系统: sudo reboot

确认下Nouveau是已经被你干掉，使用命令： lsmod | grep nouveau

安装gcc依赖：sudo apt-get install gcc make

安装显卡：sudo sh NVIDIA-Linux-x86_64-450.80.02.run

查看是否安装成功：nvidia-smi

### cuda安装

sudo sh cuda_11.0.3_450.51.06_linux.run

只要toolkits

将cuda添加到环境变量，在~/.bashrc文件中末尾加上

```
export PATH=/usr/local/cuda-11.0/bin${PATH:+:${PATH}}
export LD_LIBRARY_PATH=/usr/local/cuda-11.0/lib64${LD_LIBRARY_PATH:+:${LD_LIBRARY_PATH}}
```



更新环境变量：source ~/.bashrc

查看cuda版本：nvcc -V

### cudnn 安装

解压安装包：tar -xf cudnn-11.0-linux-x64-v8.0.5.39.tgz

运行:

```
sudo cp cuda/include/* /usr/local/cuda-11.0/include
sudo cp cuda/lib64/libcudnn* /usr/local/cuda-11.0/lib64
sudo chmod a+r /usr/local/cuda-11.0/include/*
sudo chmod a+r /usr/local/cuda-11.0/lib64/libcudnn*
```

查看版本：

cat /usr/local/cuda-11.0/include/cudnn_version.h | grep CUDNN_MAJOR -A 2