{{indexmenu_n>4}}

# 多分区数据盘_Linux

本操作示例默认数据盘容量小于2TiB，若数据盘容量大于2TiB，请参考《2TiB磁盘分区扩容》章节。  
如果主机之前划分过多个分区，那么可以使用如下方法进行扩容。  
<WRAP center round important 60%>

### 注意：

建议您不要在云主机上对云硬盘进行分区，以免影响云硬盘的扩容。  
磁盘扩容操作前请确认，若数据盘中有数据，建议您先备份数据。  
云硬盘只有当处于可用状态时才可以扩容。由于需要卸载云硬盘，故会中断您的业务，所以请您谨慎操作。  
由于新扩容的空间是附加在虚拟磁盘末端的，所以对于多分区场景，只支持对排在最后的分区进行扩容。  
</WRAP>

-----

### 操作须知：

* 本示例使用fdisk命令作为案例，parted命令不能与fdisk命令交叉使用。  
* 本示例中，云硬盘挂载点为/dev/vdb，请您根据实际情况操作。若没有查看到相应设备，请您检查云硬盘挂载信息与状态。  

### 具体操作：

* 查看当前挂载情况、文件系统类型以及分区情况  

![](/images/userguide/extend/df-h3.png)  
    `注：lsblk命令结果显示vdb下有两个分区vdb1、vdb2，为多分区，可按照本文档所述方案扩容。其它情况请参考相应的文档进行扩容。`  



* 在操作系统与控制台中卸载云硬盘，具体步骤见卸载云硬盘章节。通过云盘控制台扩容云硬盘。

![](/images/userguide/extend/image31.jpg)  

![](/images/userguide/extend/image32.jpg)  
    
* 在控制台中挂载云硬盘，具体步骤见挂载云硬盘章节。挂载完成后，在操作系统内查看磁盘大小。  

![](/images/userguide/extend/image33.jpg)  
    
* 使用fdisk命令删除第二个分区(/dev/vdb2)并创建新分区。  

![](/images/userguide/extend/image34.jpg) 

![](/images/userguide/extend/image35.jpg)  
    `注：删除分区不会造成数据盘内数据的丢失。`  
    
* 检查文件系统，并扩容。  
    `注：不同文件系统下，检查和扩容的命令不同，请您确认自己的文件系统类型，并按照相应的操作步骤操作。`  

**ext文件系统**  

* 分别执行e2fsck –f /dev/vdb2和 resize2fs /dev/vdb2进行检查和扩容操作。  

![](/images/userguide/extend/e2fsck-duo.png)  

* 使用mount命令，重新挂载磁盘。  

![](/images/userguide/extend/mount3.png)  

**xfs文件系统**  

* 执行xfs\_repair /dev/vdb2检查文件系统。  
  
![](/images/userguide/extend/xfs_repair-duo.png)  
    
* 使用mount命令，重新挂载磁盘。

![](/images/userguide/extend/mount4.png)  
    
* 执行xfs\_growfs命令扩容。  

![](/images/userguide/extend/xfs_growfs-duo.png)

