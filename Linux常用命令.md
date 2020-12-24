# Linux部分常用命令

**1.系统信息** 
arch 显示机器的处理器架构
uname -m 显示机器的处理器架构
uname -r 显示正在使用的内核版本 
dmidecode -q 显示硬件系统部件 - (SMBIOS / DMI) 
hdparm -i /dev/hda 罗列一个磁盘的架构特性 
hdparm -tT /dev/sda 在磁盘上执行测试性读取操作 
cat /proc/cpuinfo 显示CPU info的信息 
cat /proc/interrupts 显示中断 
cat /proc/meminfo 校验内存使用 
cat /proc/swaps 显示哪些swap被使用 
cat /proc/version 显示内核的版本 
cat /proc/net/dev 显示网络适配器及统计 
cat /proc/mounts 显示已加载的文件系统 
date 显示系统日期 
cal 2020 显示2007年的日历表 

**2.关机 (系统的关机、重启以及登出 )** 
shutdown -h now 关闭系统
shutdown -h hours:minutes & 按预定时间关闭系统 
shutdown -c 取消按预定时间关闭系统 
shutdown -r now 重启
reboot 重启
logout 注销 

**3.文件和目录** 
cd  绝对路径  转入该路径为工作目录
cd .. 返回上一级目录 
cd ../.. 返回上两级目录 
pwd 显示工作路径 
ls 查看目录中的文件 
ls -l 显示文件和目录的详细资料 
ls -a 显示隐藏文件 
tree 显示文件和目录由根目录开始的树形结构
mkdir 目录名 在当前目录下创建一个目录 
rm -f 文件名 删除文件'
rmdir 目录名 删除**空目录**
rm -rf 目录名 删除目录并同时删除其内容 
mv 目录名1 目录名2 重命名/移动 一个目录 
cp file1 file2 复制一个文件 
cp -a 目录1 目录2 复制一个目录到另一个目录
ln -s 文件名 链接名 创建一个指向文件或目录的软链接 
ln 文件名 链接名 创建一个指向文件或目录的物理链接 

**4.文件搜索** 
find / -name file1 从 '/' 开始进入根文件系统搜索文件和目录 
find / -user user1 搜索属于用户 'user1' 的文件和目录 
find /usr/bin -type f -atime +20 搜索在过去20天内未被使用过的执行文件 
find /usr/bin -type f -mtime -20 搜索在20天内被创建或者修改过的文件 