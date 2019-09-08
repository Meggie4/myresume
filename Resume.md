 ## <img src="assets/info-circle-solid.svg" width="30px"> 个人信息 
* 姓名：田姣姣                      
* 性别：女
* 住址：武汉
* 电话：18271926724
* 邮箱：1224642332@qq.com

## <img src="assets/graduation-cap-solid.svg" width="30px"> 教育经历

* 硕士，华中科技大学，计算机系统结构专业，2017.09~2020.06
* 学士，武汉理工大学，计算机科学与技术专业，2013.09~2017.06

## <img src="assets/tools-solid.svg" width="30px"> 专业技能

* 英语：六级，熟悉英文阅读。
* 编程语言：熟悉C++,了解C，Go。
* 针对 linux 内核的 md 模块下的 raid5 做过相应开发
* 阅读过 leveldb 源码，针对其性能做改善优化。

## <img src="assets/briefcase-solid.svg" width="30px"> 实习经历

- **深圳市腾讯计算机系统有限公司，云架构平台部门/对象存储组，2019.06~2019.08**

    **实习介绍**

    利用GO语言，开发实现了基于亚马逊s3接口的对象存储系统，完成了文件上传、下载、删除、list的功能，并进行了后期系统的单元测试。

    **详细介绍**
    1. 整体框架：系统采用Golang的开源web框架Gin，实现S3功能，整体包含两个模块：元数据索引模块和用户数据模块。用户数据通过数据分片的形式存储，元数据索引模块用于索引分片数据。

    2. 元数据模块：元数据存储在公司的lavadb数据库中。系统通过asn1协议，与lavadb服务器进行通信，完成元数据增、删、查、list.

    3. 用户数据模块：分片数据存储在公司的BlobStorage中，系统通过thrift框架，向BlobStorage进行RPC，实现分片数据的增、删、查。
    
## <img src="assets/project-diagram-solid.svg" width="30px"> 项目经历
- **基于NVM+SSD混合介质的kv存储优化**

    **项目介绍**

    随着新型介质非易失性内存的研究，本项目引入NVM来改善系统的读写性能，同时考虑到leveldb严重的写放大问题，设计了新的compaction策略，降低系统写放大。

    **模块介绍** 

    我主要设计并实现了两个模块，用来改善系统读写性能：

    1. 内存结构优化模块。引入NVM, 采用内存映射机制以及 skiplist 数据结构实现非易失的 NVMTable, 用来替代原始的memtable结构，避免write_ahead_log. 同时实现了多个parallel NVMTable, 充分利用cpu多核的特性，加快键值对的并行写入。

    2. 新的compaction策略。允许每个sstable中含有多个键值范围可重叠的partner文件，当对level i中的SSTable a进行compaction时，通过HyperLogLog算法确定level（i+1）中，与SSTable a重叠的SSTable b, c, d文件内部的重叠率。如果重叠率超过阈值，那就执行traditional compaction过程，生成新的SSTable文件。否则执行partner compaction过程，将SSTable a中相应范围的键值对写到SSTable b的partner文件中，完成partner compaction过程。

    **完成阶段**

    正在进行编码优化。

---
- **基于固态盘阵列的浮动条带实现**  
     
    **项目介绍**

    考虑到现有的raid5阵列系统适配在固态盘阵列上，会导致数据块和校验块磨损不均衡的问题。本项目通过设计一个md模块的缓存，并在缓存中对raid5条带进行重组； 对数据块和校验块实现磨损均衡，以提高固态盘阵列性能

    **负责内容**

    我主要设计并实现了md模块的缓存，主要包含3个模块：
    1. 缓存模块。缓存中的数据以条带为单位，通过基数树对条带进行缓存。
    2. 数据读写模块。读请求通过相应的条带地址在缓存中进行查找，如果不在缓存中，那就通过查找重映射信息获取其实际的地址。针对写请求，根据相应的条带地址插入到基数树缓存中。
    3. 刷回模块。当基数树缓存空间不够时，通过 LRU 缓存替换算法，将最近最久未更新的条带下刷到条带重组模块，然后再将重组后的满条带通过 raid5 模块写到底层磁盘。
