# 1.建立.sh文件，该文件用来运行./dump1090

1.1 建立文件并编辑 
```shell
nano testboot.sh
```
1.2写入如下代码
```shell   
#!/bin/sh
while true
do
ps -ef | grep "/home/pi/dump1090-hptoa/build/./dump1090" | grep -v "grep"
if [ "$?" -eq 1 ]
then
rmmod dvb_usb_rtl28xxu rtl2832
/home/pi/dump1090-hptoa/build/./dump1090 >/dev/null 2>&1
#>/dev/null 2>&1表示重定向到回收站，即不产生输出，减小日志负担
#启动应用，修改成自己的启动应用脚本或命令
echo "process has been restarted!"
else
echo "process already started!"
fi
sleep 1
done  
```  
（注：第一行不可省略，所有.sh文件必备）

1.3给文件＋权限
```shell
chmod 777 testboot.sh
```
# 2.添加自启动
.sh中的命令只能在root用户下执行。     
2.1使用systemctl设置服务在/usr/lib/systemd/system/ 下新建文件testboot.service:
```shell
sudo nano /usr/lib/systemd/system/testboot.service
```
2.2给testboot.service文0件写入内容，如下：
```shell
[Unit]
Description=simple
[Service]
Type=oneshot
ExecStart=/home/pi/testboot.sh
[Install]
WantedBy=multi-user.target
```
2.3设置服务自启动：
```shell
sudo systemctl enable testboot.service
```
2.4测试开启服务：
```shell
sudo systemctl start testboot.service
```
2.5查看服务状态：
```shell
sudo systemctl status testboot.service
```
2.6查看进程情况：
```shell
ps -A
```
# 3 关闭日志文件
3.1 查看日志（systemd-journald）配置
```shell
sudo nano /etc/systemd/journald.conf
```
3.2 更改配置文件
```shell
Storage=none
```
3.3 重启服务
```shell
sudo systemctl restart systemd-journald
```
