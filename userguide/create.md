

# 创建云硬盘

您可以通过如下4个途径来创建云数据盘：

  - 通过云盘管理控制台创建空数据盘。
  - 通过云盘管理控制台，克隆功能来创建数据盘。请参考:
    操作指南-[clone](/storage_cdn/udisk/userguide/clone)
  - 在快照管理控制台中，通过快照创建数据盘。请参考:
    操作指南-[从快照创建数据盘](/storage_cdn/udisk/userguide/snapshot/fromsnapshottodisk)
  - 随实例一起创建数据盘。请参考:
    云主机用户手册-[briefguide](/compute/uhost/newuser/briefguide)

''注意：  
目前容量大于2T云硬盘不支持扩容、快照、方舟及按时付费方式。  
建议您不要在云主机上对云硬盘进行分区，以免影响云硬盘的扩容。  
建议您不要自建 LVM 等逻辑卷，因快照针对于独立的每个磁盘，LVM 创建使用后会导致回滚快照后数据差异。''

-----

### 具体操作：

* 在“产品与服务”下拉菜单中，选择“云主机UHost”。  

![](/images/userguide/image1.png)



* 在“云主机 UHost”中，选择“云盘管理”Tab。  

![](/images/userguide/image1.2.png)



* 点击“创建云硬盘”按钮，在弹出框中自定义硬盘名称和硬盘容量等信息，选择相应的付费方式。 

![](/images/userguide/image2.jpg)  

![](/images/userguide/image3.jpg)

云硬盘有普通云硬盘（SATA）和SSD云硬盘两种规格，也可以选择SSD云硬盘。

![](/images/userguide/udisk_ssd_create01.png)

* 点击“确定”进入付费页面，完成支付后则云硬盘创建成功。 

![](/images/userguide/image4.jpg) 

![](/images/userguide/image5.jpg)


