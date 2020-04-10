

# 单分区数据盘_Windows

如果主机之前只划分过1个分区，并使用NTFS/FAT文件系统，那么可以使用如下方法进行扩容。  

### 注意：

磁盘扩容操作前请确认，若数据盘中有数据，建议您先备份数据。  
云硬盘只有当处于可用状态时才可以扩容。由于需要卸载云硬盘，故会中断您的业务，所以请您谨慎操作。  
</WRAP>

-----

### 操作须知：

  * 本示例环境版本：
![](/images/userguide/extend/image19.jpg)  

  * 本示例中，Disk1为云硬盘挂载点，请您根据实际情况操作。若没有查看到相应设备，请您检查云硬盘挂载信息与状态。

### 具体操作：

  * 查看当前挂载情况。 
![](/images/userguide/extend/image20.jpg)  
    
  * 在操作系统与控制台中卸载云硬盘，具体步骤见卸载云硬盘章节。 
  * 通过云盘控制台扩容云硬盘。  

![](/images/userguide/extend/image21.jpg)   

![](/images/userguide/extend/image22.jpg)  

  * 在控制台中挂载云硬盘，具体步骤见挂载云硬盘章节。 
  * 挂载完成后，在操作系统内查看磁盘大小。

![](/images/userguide/extend/image23.jpg)  

  * 右键单击新分区D空白处，选择扩展卷。  

![](/images/userguide/extend/image24.jpg)  

![](/images/userguide/extend/image25.jpg)  

![](/images/userguide/extend/image26.jpg)  

![](/images/userguide/extend/image27.jpg) 
    
  * 查看扩容后分区情况。  
![](/images/userguide/extend/image28.jpg)


