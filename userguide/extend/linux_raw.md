{{indexmenu_n>1}}

# 裸盘数据盘_Linux

如果主机之前并未划分过分区，只是使用裸盘格式化使用，那么可以使用如下方法进行扩容。  
<WRAP center round important 60%>

### 注意：

磁盘扩容操作前请确认，若数据盘中有数据，建议您先备份数据。  
云硬盘只有当处于可用状态时才可以扩容。由于需要卸载云硬盘，故会中断您的业务，所以请您谨慎操作。  
</WRAP>

-----

### 操作须知：

  - 本示例环境版本：  
    ![](/storage_cdn/udisk/userguide/extend/image1.jpg)  
    本示例中，云硬盘挂载点为/dev/vdb，请您根据实际情况操作。若没有查看到相应设备，请您检查云硬盘挂载信息与状态。

### 具体操作：

  * 查看当前挂载情况、文件系统类型以及分区情况。  
    ![](/storage_cdn/udisk/userguide/extend/df-h.png)  
    `注：lsblk命令结果显示vdb下未有分区，为裸设备，可按照本文档所述方案扩容；若vdb下有分区，请参考相应的分区扩容文档。`  



  * 在操作系统与控制台中卸载云硬盘，具体步骤见卸载云硬盘章节。通过云盘控制台扩容云硬盘。  
    ![](/storage_cdn/udisk/userguide/extend/image3.jpg)  
    
    ![](/storage_cdn/udisk/userguide/extend/image4.jpg)
    
  * 在控制台中挂载云硬盘，具体步骤见挂载云硬盘章节。挂载完成后，在操作系统内查看磁盘大小。  
    ![](/images/userguide/extend/image5.jpg) 
    
  * 检查文件系统,并扩容  
    `注：不同文件系统下，检查和扩容的命令不同，请您确认自己的文件系统类型，并按照相应的操作步骤操作。`  

**ext4文件系统**  

  * 执行e2fsck –f /dev/vdb 检查文件系统。  
    ![](/images/userguide/extend/image6.jpg)  
    
  * 执行resize2fs /dev/vdb进行扩容操作。  
    ![](/images/userguide/extend/resize2fs.png)  
    
  * 使用mount命令，重新挂载磁盘。  
    ![](/images/userguide/extend/mount.png)  

**xfs文件系统**  

  * 执行xfs\_check /dev/vdb 检查文件系统。  
    ![](/images/userguide/extend/xfs_check.png)  
    
  * 或xfs\_repair /dev/vdb命令 检查文件系统。  
    ![](/images/userguide/extend/xfs_repair.png)  
    
  * 使用mount命令，重新挂载磁盘。  
    ![](/images/userguide/extend/mount2.png)  
    
  * 执行xfs\_growfs命令扩容。  
    ![](/images/userguide/extend/xfs_growfs.png)
