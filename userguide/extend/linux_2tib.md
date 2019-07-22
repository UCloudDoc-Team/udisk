{{indexmenu_n>6}}

# 2TiB数据盘分区扩容\_Linux

本操作示例针对磁盘容量大于2TiB，无法通过fdisk命令进行分区的场景，通过parted命令进行分区。如果主机磁盘容量大于2TiB，那么可以使用如下方法进行分区，扩容。  
<WRAP center round important 60%>

### 注意：

建议您不要在云主机上对云硬盘进行分区，以免影响云硬盘的扩容。  
磁盘扩容操作前请确认，若数据盘中有数据，建议您先备份数据。  
云硬盘只有当处于可用状态时才可以扩容。由于需要 云硬盘，故会中断您的业务，所以请您谨慎操作。  
使用parted命令之后及时生效，请您确认操作之后再执行命令。  
分区完成之后需要单独格式化，否则不能直接使用。  
</WRAP>

-----

### 操作须知：

  - 本示例环境版本：  
    ![](/storage_cdn/udisk/userguide/extend/cat-2tib.png)  
  - 本示例使用parted命令作为案例，parted命令不能与fdisk命令交叉使用。  
  - 本示例中，云硬盘挂载点为/dev/vdb，请您根据实际情况操作。若没有查看到相应设备，请您检查云硬盘挂载信息与状态。

### 具体操作：

\*\* 新购数据盘分区 \*\*  

  - 在控制台挂载云硬盘，具体步骤见[挂载云硬盘](https://cms.docs.ucloudadmin.com/storage_cdn/udisk/userguide/mount)章节。  
  - 挂载完成后，在操作系统内查看磁盘大小。  

![](/images/userguide/extend/fdisk-2tib.png)  

  - 使用parted命令对/dev/vdb进行分区。  
    ![](/storage_cdn/udisk/userguide/extend/parted-2tib.png)  
  - 查看磁盘分区是否成功。  
    ![](/storage_cdn/udisk/userguide/extend/lsblk-2tib.png)  
  - 格式化对应磁盘分区。  
    `注：本操作以xfs文件系统为例，如果想格式化为ext4文件系统，请执行命令mkfs.ext4 /dev/vdb1`  
    ![](/storage_cdn/udisk/userguide/extend/mkfs-2tib.png)  
  - 使用mount命令挂载磁盘。  
    ![](/storage_cdn/udisk/userguide/extend/mount-2tib-no1.png)  

\*\* 扩容大于2TiB磁盘 \*\*  

  - 查看当前挂载情况、文件系统类型及分区情况。  
    ![](/storage_cdn/udisk/userguide/extend/df-th-2tib.png)  
    `注：若为裸盘数据盘（无分区），请参考裸盘数据盘_Linux章节扩容。`  



  - 在操作系统与控制台中卸载云硬盘，具体步骤见[卸载云硬盘](https://cms.docs.ucloudadmin.com/storage_cdn/udisk/userguide/umount)章节。通过云盘控制台扩容云硬盘。  
    ![](/storage_cdn/udisk/userguide/extend/image-2tib-1.png)  
    ![](/storage_cdn/udisk/userguide/extend/image-2tib-2.png)  
  - 在控制台挂载云硬盘，具体步骤见[挂载云硬盘](https://cms.docs.ucloudadmin.com/storage_cdn/udisk/userguide/mount)章节。挂载完成后，在操作系统内查看磁盘大小。  
    ![](/storage_cdn/udisk/userguide/extend/fdisk-2tib-2.png)  
  - 使用parted命令删除原来的分区并创建新分区。  
    \* parted /dev/vdb  
    ![](/storage_cdn/udisk/userguide/extend/unit-2tib.png)  
    ![](/storage_cdn/udisk/userguide/extend/mkpart-2tib.png)  

`注：删除分区不会造成数据盘内数据的丢失。`

  - 检查文件系统、并扩容。  
    `注：不同文件系统下，检查和扩容的命令不同，请您确认自己的文件系统类型，并按照相应的操作步骤操作。`

\*\* xfs文件系统 \*\*  

  - 执行xfs\_repair /dev/vdb1检查文件系统。  
    ![](/storage_cdn/udisk/userguide/extend/xfs_repair-2tib.png)  
  - 使用mount命令，重新挂载磁盘。  
    ![](/storage_cdn/udisk/userguide/extend/mount-2tib-2.png)  
  - 执行xfs\_growfs命令扩容。  
    ![](/storage_cdn/udisk/userguide/extend/xfs_growfs-2tib.png)  

\*\* ext文件系统 \*\*  

  - 执行e2fsck –f /dev/vdb1命令检查文件系统。  
    ![](/storage_cdn/udisk/userguide/extend/e2fsck-2tib-2.png)  
  - 执行resize2fs /dev/vdb1进行扩容操作。  
    ![](/storage_cdn/udisk/userguide/extend/resize2fs-2tib-2.png)  
  - 使用mount命令，重新挂载磁盘。  
    ![](/storage_cdn/udisk/userguide/extend/mount-2tib-3.png)
