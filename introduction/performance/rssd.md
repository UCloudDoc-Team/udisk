{{indexmenu_n>2}}

## RSSD性能测试
由于压测云盘的性能时，云盘本身以及压测条件都起着重要的作用。为了充分发挥出多核多并发的系统性能，压测出RSSD云盘120万IOPS性能指标，您可以参考以下rssd_test.sh脚本：

``` 
#!/bin/bash     
numjobs=16          # 测试线程数，要求不要超过CPU核数, 默认16
iodepth=32          # 每个线程IO队列深度, 默认32
bs=4k               # 每个I/O大小, 默认4k
rw=randread         # 读写方式, 默认随机读
dev_name=vdb        # 测试的块设备名， 默认vdb

if [[ $# == 0 ]]; then
  echo "Default test: `basename $0` $numjobs $iodepth $bs $rw $dev_name"
  echo "Or you can specify paramter:"
  echo "`basename $0` numjobs iodepth bs rw dev_name"
elif [[ $# == 5 ]]; then
  numjobs=$1
  iodepth=$2
  bs=$3
  rw=$4
  dev_name=$5
else
  echo "paramter number error!"
  echo "`basename $0` numjobs iodepth bs rw dev_name"
  exit 1
fi

nr_cpus=`cat /proc/cpuinfo |grep "processor" |wc -l`
if [ $nr_cpus -lt $numjobs ];then
  echo "Numjobs is more than cpu cores, exit!"
  exit -1
fi
nu=$((numjobs+1))
cpulist=""
for ((i=1;i<10;i++))
do
  list=`cat /sys/block/${dev_name}/mq/*/cpu_list | awk '{if(i<=NF) print $i;}' i="$i" | tr -d ',' | tr '\n' ','`
  if [ -z $list ];then
    break
  fi
  cpulist=${cpulist}${list}
done
spincpu=`echo $cpulist | cut -d ',' -f 2-${nu}` # 不使用0号核
echo $spincpu
echo $numjobs
echo 2 > /sys/block/${dev_name}/queue/rq_affinity
sleep 5
# 执行fio命令
fio --ioengine=libaio --runtime=30s --numjobs=${numjobs} --iodepth=${iodepth} --bs=${bs} --rw=${rw} --filename=/dev/${dev_name} --time_based=1 --direct=1 --name=test --group_reporting --cpus_allowed=$spincpu --cpus_allowed_policy=split

```
### 测试说明 

1.根据用户的测试环境，可以指定脚本的输入参数，如果不指定，会执行默认的测试方式。

2.直接测试裸盘会破坏文件系统结构。如果云盘上已经有数据，可以设置filename=[具体的文件路径，比如/mnt/test.image]。如果没有数据，可以直接设置filename=[设备名，如本示例中的/dev/vdb]

### 脚本解读

#### 块设备参数

  * 测试实例时，脚本中的命令echo 2 > /sys/block/vdb/queue/rq_affinity是将云主机实例中的块设备中的参数rq_affinity值修改为 2。
  
  * 参数rq_affinity 的值为 1 时，表示块设备收到 I/O 完成（I/O Completion）的事件时，这个 I/O 被发送回处理这个 I/O 下发流程的 vCPU 所在 Group 上处理。在多线程并发的情况下，I/O Completion 就可能集中在某一个 vCPU 上执行，这样会造成瓶颈，导致性能无法提升。
  
  * 参数rq_affinity 的值为 2 时，表示块设备收到 I/O Completion 的事件时，这个 I/O 会在当初下发的 vCPU 上执行。在多线程并发的情况下，就可以完全充分发挥各个 vCPU 的性能。

#### 绑定对应的 vCPU

  * 普通模式下，一个设备（Device）只有一个请求列表（Request-Queue）。在多线程并发处理I/O的情况下，这个唯一的Request-Queue就是一个性能瓶颈点。
  
  * 最新的多队列（Multi-Queue）模式下，一个设备（Device）可以拥有多个处理I/O的Request-Queue，可以充分发挥后端存储的性能。如果您有4个I/O线程，您需要将4个线程分别绑定在不同的Request-Queue对应的CPU Core上，这样就可以充分利用Multi-Queue提升性能。
  
  * fio 提供了参数 cpusallowed 以及 cpus_allowed_policy 来绑定 vCPU。以vdb云盘为例，运行 ls /sys/block/vdb/mq/ 查看设备名为vdb云盘的QueueId，运行 cat /sys/block/vdb/mq/$QueueId/cpu_list 查看对应设备名为vdb云盘的QueueId绑定到的cpu_core_id。

