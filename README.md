cdnfly-kaixin
仅支持CENTOS7 web目录为云端验证文件，请自行搭建

wget https://raw.githubusercontent.com/Steady-WJ/cdnfly-kaixin/main/web/web.tar.gz

tar -zxvf web.tar.gz

0.0.0.0改成(自己搭建的验证服务器IP) nano /etc/hosts

0.0.0.0 auth.cdnfly.cn

主控登录地址为: http://主控IP/ 管理员账号和密码： wenjian/wenjian 普通用户账号和密码： ceshi/ceshi

v5.1.13主控

curl -fsSL https://github.com/Steady-WJ/cdnfly-kaixin/raw/main/master.sh -o master.sh && chmod +x master.sh && ./master.sh --es-dir /home/es

v5.1.16被控

curl -fsSL https://github.com/Steady-WJ/cdnfly-kaixin/raw/main/agent.sh -o agent.sh && chmod +x agent.sh && ./agent.sh --master-ver v5.1.13 --master-ip --es-ip --es-pwd

问题解决--持续更新中：

1、普通用户的账户中心中的bug修复

cd /opt/cdnfly/master/panel/src/views/account

删除三个文件：balance、log、order

然后用下面命令重新生成软链接

ln -s ../finance/balance/ balance

ln -s ../system/log/ log

ln -s ../finance/order/ order

2、删除/tmp/send_log/

rm -rf /tmp/send_log/

3、因为开心版的带宽和流量统计是每秒执行一次任务，所以需要替换成每隔5分钟执行一次任务

在/opt/cdnfly/master/tasks目录中覆盖bandwidth_monitor.so文件

4、添加新节点显示同步cc_filter nginx openresty等错误 或者 重启节点显示filebeat: ERROR (spawn error)

chown root /opt/cdnfly/agent/conf/filebeat.yml

chmod 0644 /opt/cdnfly/agent/conf/filebeat.yml

supervisorctl restart all

5、修改主控和节点的cron任务

crontab -e

*/10 * * * * /usr/sbin/ntpdate -u pool.ntp.org > /dev/null 2>&1 || (date_str=/usr/bin/date '+%Y-%m-%d %H:%M:%S' && timedatectl set-ntp false && echo $date_str && timedatectl set-time "$date_str")
