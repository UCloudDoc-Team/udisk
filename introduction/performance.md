

# 产品性能

## 性能指标

评估云硬盘性能有3个重要的指标数据：

  - IOPS：每秒读写次数。
  - 吞吐量：每秒读写IO流量。
  - IO时延：IO提交到IO完成的时间。

理论上， IOPS与吞吐量越大越好，时延越低越好。

\*\* IOPS \*\*  
IOPS（Input/Output Operations Per
Second）是一个用于计算机存储设备（如硬盘（HDD）、固态硬盘（SSD）或存储区域网络（SAN））性能测试的量测方式，可以视为是每秒的读写次数。
IOPS根据测试倾向性的不同，主要包括4种类型的IOPS指标:随机读IOPS、随机写IOPS、顺序读IOPS、顺序写IOPS。

| IOPS类型  | 说明          |
| ------- | ----------- |
| 随机读IOPS | 每秒平均的随机读取次数 |
| 随机写IOPS | 每秒平均的随机写入次数 |
| 顺序读IOPS | 每秒平均的顺序读取次数 |
| 顺序写IOPS | 每秒平均的顺序写入次数 |

## 吞吐量

吞吐量是磁盘在单位时间内能成功传递的平均数据量。吞吐量的单位通常表示为MB每秒（MB/s或MBps）。

## IO时延

IO时延是指一次IO请求发出，到该IO请求完成所耗费的时间。

## 性能对比

UDisk主要包括3种类型的产品:RSSD云盘，SSD云盘和普通云盘

\* RSSD云盘: 底层以Nvme SSD为存储介质，网络传输使用RDMA \* SSD云盘: 底层以Nvme固态硬盘作为存储介质 \*
普通云盘: 底层以HDD机械磁盘作为存储介质

三种产品的云盘的性能对比如下表所示:

| 参数     | RSSD云盘（公测中）                 | SSD云盘                     | 普通云盘        |
| ------ | --------------------------- | ------------------------- | ----------- |
| 单盘IOPS | min{1800+50\\\*容量，1200000}  | min{1200+30\\\*容量，24000}  | 1000(峰值)    |
| 单盘吞吐量  | min{120+0.5\\\*容量，4800}MBps | min{80+0.5\\\*容量，260}MBps | 100MB/s(最大) |
| 平均时延   | 0.1-0.2ms                   | 0.5-3ms                   | 10ms        |

## 性能测试

\*\* 测试工具 \*\*  
使用[fio](https://github.com/axboe/fio)工具，建议使用libaio引擎测试

\*\* 安装方法 \*\*  
Linux: yum install fio.x86\_64

\*\* 测试方法 \*\*  

  - \*\* 随机读 \*\*  
    fio -direct=1 -iodepth=128 -rw=randread -ioengine=libaio -bs=4k
    -size=200G -numjobs=1 -runtime=1000 -group\_reporting -name=test
    -filename=/data/test



  - \*\* 随机写 \*\*  
    fio -direct=1 -iodepth=128 -rw=randwrite -ioengine=libaio -bs=4k
    -size=200G -numjobs=1 -runtime=1000 -group\_reporting -name=test
    -filename=/data/test



  - \*\* 顺序读 \*\*  
    fio -direct=1 -iodepth=128 -rw=read -ioengine=libaio -bs=4k
    -size=200G -numjobs=1 -runtime=1000 -group\_reporting -name=test
    -filename=/data/test



  - \*\* 顺序写 \*\*  
    fio -direct=1 -iodepth=128 -rw=write -ioengine=libaio -bs=4k
    -size=200G -numjobs=1 -runtime=1000 -group\_reporting -name=test
    -filename=/data/test

\*\* fio参数说明 \*\*  
^ 参数 ^ 说明 ^

|                       |                                                                          |
| --------------------- | ------------------------------------------------------------------------ |
| \-direct=1            | 忽略缓存，直接写入磁盘                                                              |
| \-iodepth=128         | 请求的IO队列深度                                                                |
| \-rw=write            | 读写策略，可选值randread(随机读)，randwrite(随机写)，read(顺序读)，write(顺序写)，randrw(混合随机读写) |
| \-ioengine=libaio     | IO引擎配置，建议使用libaio                                                        |
| \-bs=4k               | 块大小配置，可以使用4k，8k，16k等                                                     |
| \-size=200G           | 测试生成文件的大小                                                                |
| \-numjobs=1           | 线程数配置                                                                    |
| \-numtime=1000        | 测试运行时长，单位秒                                                               |
| \-group\_reporting    | 测试结果汇总展示                                                                 |
| \-name=test           | 测试任务名称                                                                   |
| \-filename=/data/test | 测试输出的路径与文件名                                                              |

常见测试用例如下：  
\* 时延性能测试：  
fio -direct=1 -iodepth=1 -rw=read -ioengine=libaio -bs=4k -size=200G
-numjobs=1 -runtime=1000 -group\_reporting -name=test
-filename=/data/test  
\* 吞吐性能测试：  
fio -direct=1 -iodepth=128 -rw=write -ioengine=libaio -bs=256k
-size=200G -numjobs=1 -runtime=1000 -group\_reporting -name=test
-filename=/data/test  
fio -direct=1 -iodepth=128 -rw=read -ioengine=libaio -bs=256k -size=200G
-numjobs=1 -runtime=1000 -group\_reporting -name=test
-filename=/data/test  
\* IOPS性能测试(4k，128队列，随机读写)：  
fio -direct=1 -iodepth=128 -rw=randwrite -ioengine=libaio -bs=4k
-size=200G -numjobs=1 -runtime=1000 -group\_reporting -name=test
-filename=/data/test  
fio -direct=1 -iodepth=128 -rw=randread -ioengine=libaio -bs=4k
-size=200G -numjobs=1 -runtime=1000 -group\_reporting -name=test
-filename=/data/test  

## RSSD性能和实例性能关系

虚机实例的IO性能与其CPU配置成正比线性关系，虚机核数越多可获得的存储IOPS和吞吐量越高  
\* 如果RSSD云盘的性能不超过实例对应的IO存储能力，实际存储性能以RSSD云盘性能为准  
\* 如果RSSD云盘的性能超过了实例对应的IO存储能力，实际存储性能以该实例对应的存储性能为准  
\* 如果实例核数不在下表中，则实例性能是为不超过该核数的最大性能，例如CPU核数为50，则其存储IO性能与48核相同  

| vCPU（核） | 存储IOPS（万） | 存储吞吐量（MBps） |
| ------- | --------- | ----------- |
| 1       | 1.8       | 75          |
| 2       | 3.8       | 150         |
| 4       | 7.5       | 300         |
| 8       | 15        | 600         |
| 12      | 22.5      | 900         |
| 16      | 30        | 1200        |
| 24      | 45        | 1800        |
| 32      | 60        | 2400        |
| 48      | 90        | 3600        |
| 64      | 120       | 4800        |
