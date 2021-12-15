### top命令

```bash
top命令
[root@fpm_nginx /app]# top
top - 12:19:58 up  3:45,  1 user,  load average: 0.00, 0.02, 0.05
Tasks:  91 total,   1 running,  90 sleeping,   0 stopped,   0 zombie
%Cpu(s):  0.3 us,  0.3 sy,  0.0 ni, 99.3 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
KiB Mem :   995896 total,   695140 free,    94524 used,   206232 buff/cache
KiB Swap:  2097148 total,  2097148 free,        0 used.   731568 avail Mem 

   PID USER      PR  NI    VIRT    RES    SHR S %CPU %MEM     TIME+ COMMAND                          
  6370 root      20   0  573936  17236   6096 S  0.3  1.7   0:05.52 tuned                            
  9977 root      20   0  161892   2172   1548 R  0.3  0.2   0:00.73 top      
  
第一行:
top - 12:19:58 up  3:45				# 启动了3小时45分钟，当前时间系统时间-12:19:58
1 user							 	# 同时在线的用户
load average: 0.00, 0.02, 0.05			# 服务器的负载，1min的负载、5min的负载、15min的负载

第二行:
Tasks:  91 total					# 当前有91个工作任务
1 running							# 1个正在执行的
90 sleeping							# 90个在休眠的
0 stopped							# 没有被停止的
0 zombie							# 没有僵尸进程

第三行:
%Cpu(s):  0.3 us,  0.3 sy			# 用户使用cpu的百分比
		us		user		# 用户态
		sy		system		# 内核态

ni									# 优先级
99.3 id								# cpu空闲程度
0.0 wa								# 等待的状态的进程占cpu的百分比
0.0 hi								# 硬中断
0.0 si								# 软中断
0.0 st								# 虚拟机占用物理机的百分比
```





## CPU 相关监控项

- us：用户空间占用CPU百分比（Host.cpu.user）
- sy：内核空间占用CPU百分比（Host.cpu.system）
- ni：用户进程空间内改变过优先级的进程占用CPU百分比
- id：空闲CPU百分比（Host.cpu.idle）
- wa：等待输入输出的CPU时间百分比
- hi：硬件中断
- si：软件中断
- st：实时

| 监控项名称         | 监控项含义                | 单位 | 说明                                                         |
| ------------------ | ------------------------- | ---- | ------------------------------------------------------------ |
| Host.cpu.idle      | 当前空闲CPU百分比         | %    | 当前CPU处于空闲状态的百分比                                  |
| Host.cpu.system    | 当前内核空间占用CPU百分比 | %    | 指系统上下文切换的消耗,该监控项数值比较高，说明服务器开了太多的进程或者线程 |
| Host.cpu.user      | 当前用户空间占用CPU百分比 | %    | 用户进程对CPU的消耗                                          |
| Host.cpu.iowait    | 当前等待IO操作的CPU百分比 | %    | 该项数值比较高说明有很频繁的IO操作                           |
| Host.cpu.other     | 其他占用CPU百分比         | %    | 其他消耗，计算方式为（Nice + SoftIrq + Irq + Stolen）的消耗  |
| Host.cpu.totalUsed | 当前消耗的总CPU百分比     | %    | 指以上各项CPU消耗的总和，通常用于报警                        |



```bash
[root@fpm_nginx ~]# yum -y install htop
htop
top

-d		# 指定动态变化时间
-p		# 查看指定pid的进程
-u		# 查看指定用户的进程
-b		# 输出完整的内容
	top -d1  -b -n2 >top.txt
-n		# 指定次数

# top的快捷键
h		# 查看帮助
z		# 高亮显示
1 		# 显示所有cpu
s		# 设置刷新时间
b		# 高亮显示处于R状态的进程
M		# 按内存百分比排序输出
P		# 按CPU使用百分比排序输出
R		# 对排序进行反转
f		# 自定义显示字段
k		# kill掉指定的pid进程
W		# 保存top环境设置 ~/.toprc
B		#  加粗
q		# 退出

PID 	# 进程id
USER      # 用户
PR  	# 优先级
NI    	# nice值
VIRT    # 虚拟内存
RES    # 真实内存
SHR 	# 共享内存
S 		# 进程的状态
%CPU	# 占用cpu的百分比
%MEM 	# 占用内存的百分比
TIME+ 	# 进程运行时间
COMMAND		# 进程运行的命令
```





则按shift+p，将进程按照CPU占用率从高到低排序