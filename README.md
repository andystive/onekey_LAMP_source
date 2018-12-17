# **onekey_LAMP_Script**
## *本脚本为一键部署Apache、PHP、Mariadb、phpMyadmin、Wordpress脚本，所有软件均采用源码包安装。*
### **注意事项：**
1. 脚本默认无执行权限，需执行 chmod +x V3.0-onekey_LAMP+wordpress.sh 赋予脚本执行权限
2. 本脚本适用于centos7系统，软件均采用源码包编译安装，小内存主机安装时间可能接近1小时
3. 源码包默认存放位置为 /usr/local/src/
4. 软件默认安装位置为 /usr/local/
5. 源码包下载速度取决于网络环境，source文件夹为下载好的源码包，可以直接上传到 /usr/local/src/，脚本会自动检测并跳过下载
6. 脚本执行时，会创建临时swap分区，防止小内存云主机环境下安装，因内存不足导致mysql编译安装失败，安装后默认会删除
7. 执行数据库初始化时若提示输入当前数据库root密码，直接回车便可，然后根据提示设置root密码
8. wordpress安装时默认创建名为wordpress的数据库，用户名默认为root，密码输入之前设置的数据库root密码即可
9. 登录浏览器 http://主机IP/wordpress/ 创建wordpress站点标题、用户及密码便可正常使用。
10. 登录浏览器 http://主机IP/phpmyadmin/ 输入MySQL数据库账号和密码后即可正常管理数据库
