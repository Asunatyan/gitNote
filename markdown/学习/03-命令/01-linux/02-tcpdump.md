### tcpdump命令格式

tcpdump \[ -DenNqvX ]\[ -c count ] \[ -F file ]\[ -i interface ]\[ -r file ]\[ -s snaplen ]\[ -w file ][ expression ]

![1639618639376](../../../../pic/markdown/1639618639376.png)

![1639618691172](../../../../pic/markdown/1639618691172.png)

![1639618716102](../../../../pic/markdown/1639618716102.png)



![1639618760986](../../../../pic/markdown/1639618760986.png)



tcpdump port 8916 -i any -s0 -w dump.8916
抓包8916端口，放到dump.8916文件
使用Wireshark来查看文件