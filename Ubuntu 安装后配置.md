# Ubuntu 18.04安装后配置

1. 安装搜狗拼音，直接搜索官网下载安装，然后重启OK

2. 安装vim   $ sudo apt-get install vim

3. 更换源

   ```
   // 备份源
   $  sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak
   // 在/etc/apt/sources.list文件前面添加如下条目
   #阿里源
   deb http://mirrors.aliyun.com/ubuntu/ bionic main restricted universe multiverse
   deb http://mirrors.aliyun.com/ubuntu/ bionic-security main restricted universe multiverse
   deb http://mirrors.aliyun.com/ubuntu/ bionic-updates main restricted universe multiverse
   deb http://mirrors.aliyun.com/ubuntu/ bionic-proposed main restricted universe multiverse
   deb http://mirrors.aliyun.com/ubuntu/ bionic-backports main restricted universe multiverse
   deb-src http://mirrors.aliyun.com/ubuntu/ bionic main restricted universe multiverse
   deb-src http://mirrors.aliyun.com/ubuntu/ bionic-security main restricted universe multiverse
   deb-src http://mirrors.aliyun.com/ubuntu/ bionic-updates main restricted universe multiverse
   deb-src http://mirrors.aliyun.com/ubuntu/ bionic-proposed main restricted universe multiverse
   deb-src http://mirrors.aliyun.com/ubuntu/ bionic-backports main restricted universe multiverse
   
   // 更新
   $ sudo apt-get update 
   $sudo apt-get upgrade
   
   ```

   

4. 安装chrmoe

5. 时间同步 $ sudo timedatectl set-local-rtc 1

6. 取消Ubuntu自动更新

   

   ## 常用快捷键

   - 快速打开终端 ctrl+alt+t
   - 查看面板win
   - 快速切桌面win+d

   