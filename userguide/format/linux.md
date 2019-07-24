{{indexmenu_n>1}}

# Linux

<WRAP center round important 60%>

### 注意：

\*\* 磁盘格式化操作前请确认，若数据盘中有数据，建议您先备份数据。\*\*  
\*\* 建议您不要在云主机上对云硬盘进行分区，以免影响云硬盘的扩容。\*\*

</WRAP>

### 操作须知：

  * 本示例云盘数据盘挂载点：  
    ![](/storage_cdn/udisk/userguide/format/image1.jpg)



  * 本示例环境版本：  
    ![](/storage_cdn/udisk/userguide/format/image2.jpg)

### 具体操作：

  * 通过页面console或SSH工具连接主机实例，本示例使用自有SSH工具。
  * 登陆主机实例后，使用fdisk -l命令查看云主机的硬盘分区。  
    ![](/storage_cdn/udisk/userguide/format/image3.jpg)

`本示例中，云硬盘挂载点为/dev/vdb，请您根据实际情况操作。若没有查看到相应设备，请您检查云硬盘挂载信息与状态。`

  * 创建文件系统，使用命令mkfs.ext4 /dev/vdb。  



    <WRAP center round important 60%>

**会格式化本盘已有数据,操作前请确认若数据盘中有数据，请先备份数据** </WRAP>

![](/images/userguide/format/image4.jpg)
`本示例中，使用ext4文件系统，您可以自行选择所需文件系统格式。`

  * 检查执行结果，使用命令parted /dev/vdb。

![](/images/userguide/format/image5.jpg)

  * 修改/etc/fstab文件，使数据盘能随系统启动自动挂载。  
    使用命令echo UUID="910d2922-38f2-4b16-b5cc-a55d2c60cb3c" /mnt ext4
    defaults 0 0 \>\> /etc/fstab。

![](/images/userguide/format/image6.jpg)

![](/images/userguide/format/image7.jpg)
查看UUID命令。推荐使用UUID来配置挂载信息，避免linux盘符分配机制中可能会造成的盘符漂移现象。

  * 使用mount命令挂载磁盘。  
    ![](/storage_cdn/udisk/userguide/format/image8.jpg)
