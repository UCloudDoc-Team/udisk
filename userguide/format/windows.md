

# Windows

<WRAP center round important 60%>

### 注意：

**磁盘格式化操作前请确认，若数据盘中有数据，建议您先备份数据。** </WRAP>

-----

### 操作须知：

* 本示例环境版本：  
![](/images/userguide/format/image9.jpg)

### 具体操作：

* 通过页面console或RDC工具连接主机实例，本示例使用自有RDC工具。
* 在Windows Server桌面，右键单击“开始”图标，选择“磁盘管理”。  

![](/images/userguide/format/image10.jpg)  

![](/images/userguide/format/image11.jpg)  
    `本示例中，Disk1为云硬盘挂载点，请您根据实际情况操作。若没有查看到相应设备，请您检查云硬盘挂载信息与状态。`



* 在Disk1上右键点击，选择“初始化磁盘”。

![](/images/userguide/format/image12.jpg)  
    `若磁盘为offline状态，请您先online磁盘。`

![](/images/userguide/format/image13.jpg)  
''磁盘大于 2TB 时仅支持 GPT 分区形式。若您不确定磁盘后续扩容是否会超过该值，则建议您选择 GPT 分区；  
若您确定磁盘大小不会超过该值，则建议您选择 MBR 分区以获得更好的兼容性。''

![](/images/userguide/format/image14.jpg)

* 对磁盘分区，在未分配的空间处右击选择“新建简单卷”。  

![](/images/userguide/format/image15.jpg)  

在弹出的“新建简单卷向导”窗口中，点击下一步： 

![](/images/userguide/format/image16.jpg)  

输入分区所需磁盘大小，点击下一步。  

![](/images/userguide/format/image17.jpg) 

分配驱动器号（盘符），点击下一步。  

![](/images/userguide/format/image18.jpg)  

选择文件系统，格式化分区，点击下一步。  

![](/images/userguide/format/image19.jpg) 

点击完成。  

![](/images/userguide/format/image20.jpg)  

* 查看新分区情况。  

![](/images/userguide/format/image21.jpg)

![](/images/userguide/format/image22.jpg)


