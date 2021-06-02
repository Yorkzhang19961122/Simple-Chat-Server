### 项目开发环境搭建

#### 1. Ubuntu Linux环境

* 下载安装VMware Workstation，下载Ubuntu18.04镜像文件，用VMware创建一个Ubuntu虚拟机
* 在Ubuntu中打开一个terminal，使用命令`apt-get install openssh-server`安装**ssh-server**
* 验证ssh服务安装成功：使用命令`sudo netstat -tanp`查看是否有sshd服务
* 安装JSON库
  * 直接包含JSON.hpp头文件即可
* 安装boost库和muduo网络库
  * 安装boost库：[参考链接](https://blog.csdn.net/QIANGWEIYUAN/article/details/88792874)，注意最后测试代码使用`g++ test.cpp -o test`和`./test`进行编译运行
  * 安装muduo库：[参考链接](https://blog.csdn.net/QIANGWEIYUAN/article/details/89023980)
* 安装redis
  * `sudo apt-get install redis-server`
  * 安装完成后默认运行在6379端口
* 安装MySQL数据库
  * `sudo apt-get install mysql-server`
  * 安装完成后默认运行在3306端口
  * 使用`mysql -u root -pszx123`登录MySQL
  * 使用`exit`命令退出MySQL
* 安装Nginx
  * [TODO] 后续使用再安装

#### 2. Windows+VSCode配置远程Linux开发环境

* 远程主机需要安装**ssh-server**（第一步中已安装）

* 本地主机安装**ssh-client**（只要本地有git服务即可），本地安装VSCode

* 打开VSCode，安装**Remote Development**插件

* 打开插件，左上角选择SSH Targets，点击齿轮，配置ssh：

  ```c
  # Read more about SSH config files: https://linux.die.net/man/5/ssh_config
  Host 填写远程主机名
      HostName 远程主机的ip地址
      User 远程主机的用户名
  ```

  输入完成后保存，左侧会出现配置好的信息，右键登陆即可（需要输入远程主机的密码）

* 在local和远程主机上安装C/C++和Code Runner等插件

至此，聊天服务器项目的开发环境基本搭建完毕。

