title: Windows10与Ubuntu15.10双系统安装
date: 2016-03-02 00:00:00
categories: System
tags: [Windows10, Ubuntu15.10]
description: 对于全新PC,首先安装好Windows10,再安装Ubuntu15.10<br><br><img src="/images/ubuntu1510/ubuntu1510.png" alt="Ubuntu"><br>详细安装步骤请阅读全文
---

## 1. 安装Windows10  
全新格式化时注意多分空间到磁盘0，即C盘，因为安装Ubuntu时需要从C盘中压缩磁盘来获取空间。  

## 2. Ubuntu安装前准备  

### 2.1 获取Ubuntu空间  
从Windows10的C盘压缩磁盘用于安装Ubuntu，至少50GB，如果以后集群要处理大规模数据，应该尽可能的多分出空间到Ubuntu中。  

### 2.2 下载EasyBCD软件，并安装  
1. 添加新条目
2. 选择NeoGrub
3. 安装
4. 配置
5. 出现menu.lst文件，填写如下内容  
	title Install Ubuntu  
	root (hd0,0)  
	kernel (hd0,0)/vmlinuz boot=casper iso‐scan/filename=/ubuntu.iso 
	ro quiet splash locale=zh_CN.UTF‐8
	initrd (hd0,0)/initrd.lz  
ubuntu.iso改为你下载的ubuntu的iso名字  

### 2.3 解压缩ubuntu.iso文件  
windows系统默认自带装载iso文件的功能，装载成功后将里面文件initrd.lz、vmlinuz文件和.disk文件夹复制到C盘根目录，同时将ubuntu.iso也拷贝到C盘根目录。（注意：如果是64位版本的ubuntu，vmlinuz文件可能为vmlinuz.efi，把vmlinuz.efi的文件名改成vmlinuz）  

### 2.4 重启Windows即可看到系统给出两个启动菜单  

## 3. 安装Ubuntu  

### 3.1 选择NeoGrub引导加载器  
进入到Ubuntu后，默认桌面有2个文件，一个是演示，不用管。另一个是安装Ubuntu，先不点。打开终端Ctrl+Alt+T，输入命令,取消掉对光盘所在驱动器的挂载，否则分区界面找不到分区：  
	
	sudo umount -i /isodevice  

### 3.2 点击桌面上的“安装Ubuntu15.10”  
1. 安装类型选择“其他选项”
2. 分区：
	1. 根分区“/”，50G，ext4,逻辑分区
	2. /boot分区，4G，如果计算机中有多个磁盘，那么Ubuntu的/boot应该和Windows的引导程序在同一个磁盘上。另外的/，/Swap，/home可以在另一块磁盘上。 
	3. /SWAP，交换空间，4G
	4. /home 剩余部分  
3. 安装启动引导器的设备选择根磁盘位置即可
4. 点击现在安装即可完成  

## 4. 后续清理  
1. 进入Windows,打开EasyBCD,点击编辑引导菜单，删除“Ubuntu的安装引导项”
2. 删除C盘下的vmlinuz,initrd.lz和Ubuntu.iso文件。
3. 可在EasyBCD中设置默认开机选项
 
     
   