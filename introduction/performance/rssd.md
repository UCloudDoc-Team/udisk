{{indexmenu_n>2}}

# 性能测试
#### 测试工具
使用[fio](https://github.com/axboe/fio)工具，建议使用libaio引擎测试
#### 安装方法
Linux: yum install fio.x86_64    

#### fio参数说明
| 参数 | 说明 |
| ------ | ------ |
| -direct=1 | 忽略缓存，直接写入磁盘 |
| -iodepth=128 | 请求的IO队列深度 |
| -rw=write | 读写策略，可选值randread(随机读)，randwrite(随机写)，read(顺序读)，write(顺序写)，randrw(混合随机读写) |
| -ioengine=libaio | IO引擎配置，建议使用libaio |
| -bs=4k | 块大小配置，可以使用4k，8k，16k等 |
| -size=200G | 测试生成文件的大小 |
| -numjobs=1 | 线程数配置 |
| -numtime=1000 | 测试运行时长，单位秒 |
| -group_reporting | 测试结果汇总展示 |
| -name=test | 测试任务名称 |
| -filename=/data/test | 测试输出的路径与文件名 |
    
常见测试用例如下：

* 时延性能测试：
```
fio -direct=1 -iodepth=1 -rw=read -ioengine=libaio -bs=4k -size=200G -numjobs=1 -runtime=1000 -group_reporting -name=test -filename=/data/test
```
* 吞吐性能测试：
```
fio -direct=1 -iodepth=32 -rw=write -ioengine=libaio -bs=256k -size=200G -numjobs=4 -runtime=1000 -group_reporting -name=test -filename=/data/test
fio -direct=1 -iodepth=32 -rw=read -ioengine=libaio -bs=256k -size=200G -numjobs=4 -runtime=1000 -group_reporting -name=test -filename=/data/test
```

* IOPS性能测试(4k，4*32队列，随机读写)：
```
fio -direct=1 -iodepth=32 -rw=randwrite -ioengine=libaio -bs=4k -size=200G -numjobs=4 -runtime=1000 -group_reporting -name=test -filename=/data/test
fio -direct=1 -iodepth=32 -rw=randread  -ioengine=libaio -bs=4k -size=200G -numjobs=4 -runtime=1000 -group_reporting -name=test -filename=/data/test
```

### RSSD性能和实例性能关系

虚机实例的IO性能与其CPU配置成正比线性关系，虚机核数越多可获得的存储IOPS和吞吐量越高

* 如果RSSD云盘的性能不超过实例对应的IO存储能力，实际存储性能以RSSD云盘性能为准
* 如果RSSD云盘的性能超过了实例对应的IO存储能力，实际存储性能以该实例对应的存储性能为准
* 如果实例核数不在下表中，则实例性能是为不超过该核数的最大性能，例如CPU核数为50，则其存储IO性能与48核相同。

|vCPU（核） |存储IOPS（万）|存储吞吐量（MB/s）|
| ------ |-----| ------ |
| 1 |1.8|75|
| 2 |3.8|150|
| 4 |7.5|300|
| 8 |15|600|
| 12 |22.5|900|
| 16 |30|1200|
| 24 |45|1800|
| 32 |60|2400|
| 48 |90|3600|
| 64 |120|4800|

### RSSD性能测试
由于压测云盘的性能时，云盘本身以及压测条件都起着重要的作用。为了充分发挥出多核多并发的系统性能，压测出RSSD云盘120万IOPS性能指标，您可以参考以下rssd_test.sh脚本：

```    
function RunFio
{
 numjobs=$1  # 实例中的测试线程数，如示例中的 16
 iodepth=$2  # 同时发出I/O数的上限，如示例中的 32
 bs=$3       # 单次I/O的块文件大小，如示例中的 4K
 rw=$4       # 测试时的读写策略，如示例中的 randwrite
 filename=$5 # 指定测试文件的名称，如示例中的 /dev/vdb
 nr_cpus=`cat /proc/cpuinfo |grep "processor" |wc -l`
 if [ $nr_cpus -lt $numjobs ];then
     echo “Numjobs is more than cpu cores, exit!”
     exit -1
 fi
 let nu=$numjobs+1
 cpulist=""
 for ((i=1;i<10;i++))
 do
     list=`cat /sys/block/vdb/mq/*/cpu_list | awk '{if(i<=NF) print $i;}' i="$i" | tr -d ',' | tr '\n' ','`
     if [ -z $list ];then
         break
     fi
     cpulist=${cpulist}${list}
 done
 spincpu=`echo $cpulist | cut -d ',' -f 2-${nu}`
 echo $spincpu
 fio --ioengine=libaio --runtime=30s --numjobs=${numjobs} --iodepth=${iodepth} --bs=${bs} --rw=${rw} --filename=${filename} --time_based=1 --direct=1 --name=test --group_reporting --cpus_allowed=$spincpu --cpus_allowed_policy=split
}
echo 2 > /sys/block/vdb/queue/rq_affinity
sleep 5
RunFio 16 32 4k randwrite /dev/vdb

```
**测试说明** 

1. 因测试环境而异，脚本中您需要修改的命令行有：
    + 命令行list=`cat /sys/block/vdb/mq/*/cpu_list | awk '{if(i<=NF) print $i;}' i="$i" | tr -d ',' | tr '\n' ','`中的vdb。   
    + 命令行RunFio 8 64 4k randwrite /dev/vdb中的8、64、4k、randwrite和/dev/vdb。
2. 直接测试裸盘会破坏文件系统结构。如果云盘上的数据丢失不影响业务，可以设置filename=[设备名，如本示例中的/dev/vdb]。否则，请设置为filename=[具体的文件路径，比如/mnt/test.image]。

**脚本解读** 

*块设备参数*

  * 测试实例时，脚本中的命令echo 2 > /sys/block/vdb/queue/rq_affinity是将 ECS 实例中的块设备中的参数 rq_affinity值修改为 2。   
  * 参数 rq_affinity 的值为 1 时，表示块设备收到 I/O 完成（I/O Completion）的事件时，这个 I/O 被发送回处理这个 I/O 下发流程的 vCPU 所在 Group 上处理。在多线程并发的情况下，I/O Completion 就可能集中在某一个 vCPU 上执行，这样会造成瓶颈，导致性能无法提升。
  * 参数 rq_affinity 的值为 2 时，表示块设备收到 I/O Completion 的事件时，这个 I/O 会在当初下发的 vCPU 上执行。在多线程并发的情况下，就可以完全充分发挥各个 vCPU 的性能。

*绑定对应的 vCPU*

  * 通常模式下，一个设备（Device）只有一个请求列表（Request-Queue）。在多线程并发处理 I/O 的情况下，这个唯一的 Request-Queue 就是一个性能瓶颈点。最新的多队列（Multi-Queue）模式下，一个设备（Device）可以拥有多个处理 I/O 的 Request-Queue，可以充分发挥后端存储的性能。 
  * 如果您有 4 个 I/O 线程，您需要将 4 个线程分别绑定在不同的 Request-Queue 对应的 CPU Core 上，这样就可以充分利用 Multi-Queue 提升性能。
  * 为了充分发挥设备（Device）的性能，需要将 I/O 线程分发到不同的 Request-Queue 上处理。脚本中通过fio —ioengine=libaio —runtime=30s —numjobs=${numjobs} —iodepth=${iodepth} —bs=${bs} —rw=${rw} —filename=${filename} —time_based=1 —direct=1 —name=test —group_reporting —cpus_allowed=$spincpu —cpus_allowed_policy=split，分别将几个 jobs 绑定不同的 CPU Core 上，其中 vd 为您的云盘设备名，例如，/dev/vdb。
  * fio 提供了参数 cpusallowed 以及 cpus_allowed_policy 来绑定 vCPU。以上命令一共运行了几个 jobs，分别绑定在几个 CPU Core上，分别对应着不同的 Queue_Id。运行 ls /sys/block/vd/mq/ 查看设备名为 vd 云盘的 Queue_Id，例如 vdb。运行 cat /sys/block/vd/mq//cpu_list 查看对应设备名为 vd* 云盘的 Queue* 绑定到的 cpu_core_id。


