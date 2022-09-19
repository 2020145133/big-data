# 分布式计算框架MapReduce

预习报告

---
## 一. 什么是MapReduced
MapReduce是一种用于在大型商用硬件集群中（成千上万的结点）对海量数据（多个兆字节数据集）实施可靠的、高容错的分布式计算的框架，也是一种经典的并行计算模型。MapReduce模型适合于大文件的处理，对很多小文件的处理效率不是很高，这一点和HDFS一样。

---

## 二. MapReduce编程模型
### 1. MapReduce编程模型简介
MapReduce是一种思想或是一种编程模型。其编程模型主要由两个抽象类构成，即Mapper类和Reducer类。Mapper类用以对切分过的原始数据进行处理，Reducer则对Mapper的结果进行汇总，得到最后的输出结果。
根据工作原理，可将MapReduce编程模型分为两类：MapReduce简单模型和MapReduce复杂模型。MapReduce简单模型中只有Mapper过程，由Mapper产生的数据直接写入HDFS。而MapReduce复杂模型则有Mapper过程和Reducer过程。
    
### 2. MapReduce编程实例

---

## 三. MapReduce数据流
### 1. 分片、格式化数据源（InputFormat）
InputFormat主要有两个任务，一个是对源文件进行分片，并确定Mapper的数量；另一个是对各分片进行格式化，处理成<key,value>形式的数据流并传给Mapper。
### 2. Map过程
    Mapper接收<key,value>形式的数据，并处理成<key,value>形式的数据，具体的处理过程可由用户定义。
### 3. Combiner过程
用于对map()端的输出先做一次合并，以减少在Map和Reduce结点之间的数据传输量，提高网络I/0性能，是MapReduce的一种优化手段之一。
### 4. Shuffle过程
是指从Mapper产生的直接输出结果，经过一系列的处理，成为最终的Reduce直接输入数据为止的整个过程，这一过程也是MapReduce的核心过程。该过程可分为两个阶段，Mapper端的Shuffle和Reduce端的Shuffle。
### 5. Reduce过程
    Reduce接收<key,{value list}> 形式的数据流，形成<key,value>形式的数据输出，输出数据直接写入HDFS，具体的处理过程可由用户定义。

---
## 四. MapReduce任务运行流程
### 1. MRv2基本组成
MRv2舍弃了MRv1中的JobTrack和TaskTrack，而采用一种新的MRAppMaster进行单一任务管理。由客户端（client）、MRAppMaster、Map Task和Reduce Task组成。
### 2. Yarn基本组成
是一个资源管理平台，监控和调度整个集群资源，并负责管理集群所有任务的运行和任务资源的分配。由Resource Manager（RM）、NodeManager、ApplicationMaster（AM）、container。
### 3. 任务流程
1). client向ResourceManager提交任务。
2). RggesourceManager分配该任务的第一个container，并通知相应的NodeManager启动RAppMaster。
3). NodeManager接收命令后，开辟一个container资源空间，并在container中启动相应的MRAppMaster。
4). MRAppMaster启动之后，第一步会向ResourceManager注册，这样用户可以直接通过MRAppMaster监控任务的运行状态；之后则直接由MRAppMaster调度任务运行，重复（e-h）,直到任务结束。
5). MRAppMaster以轮询的方式向ResourceManager申请任务运行所需的资源。
6). 一旦ResourceManager配给了资源，MRAppMaster便会与相应的NodeManager通信，让它划分Container并启动相应的任务（Map Task或Reduce Task）。
7). NodeManager准备好运行环境，启动任务。
8). 各任务运行，并定时通过RPC协议向MRAppMaster汇报自己的运行状态和进度。MRAppMaster也会实时地监控任务的运行，当发现某个Task假死或失败时，便杀死它重新启动任务。
9). 任务完成，MRAppMaster向ResourceManager通信，注销并关闭自己。


