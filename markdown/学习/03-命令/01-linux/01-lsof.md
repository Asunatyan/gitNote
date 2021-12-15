**lsof（list open files）是一个列出当前系统打开文件的工具**

lsof -nP -i6TCP:端口号

- -n 表示不显示主机名
- -P 表示不显示端口俗称
- -TCP/UDP 
- -6/4  ip6/4







[root@iot-test-1 ~]# netstat -aonp | grep tcp| wc -l
[root@iot-test-1 ~]# lsof -iTCP | wc -l