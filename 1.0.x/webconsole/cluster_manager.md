# 添加服务与多集群管理



<video id="video" length=1000 width=800 controls="" preload="none" poster="http://jungle111111.cn-bj.ufileos.com/usdp-1.0.0.0/video/poster/17.USDP%E5%A4%9A%E9%9B%86%E7%BE%A4%E7%AE%A1%E7%90%86.png">
      <source id="mp4" src="http://jungle111111.cn-bj.ufileos.com/usdp-1.0.0.0/video/mp4/17.USDP%E6%B7%BB%E5%8A%A0%E6%9C%8D%E5%8A%A1%E4%B8%8E%E5%A4%9A%E9%9B%86%E7%BE%A4%E7%AE%A1%E7%90%86.mp4">
</video>



在使用USDP管理大数据业务系统时，用户可根据业务需求，来规划所需的大数据集群数量及单集群的规模和服务；根据业务形态，决策集群间的隔离性。

![](../../images/1.0.x/webconsole/clusters/2020123031008.png)

如上图所示，可结合网络VPC（vlan等）技术实现VPC间的互通管理，大数据集群Cluster1与Cluster2可能承载的是不同且不相关的大数据处理业务，因此无法互相访问和共享数据。



## 1. 新增大数据集群

当用户搭建好USDP服务时，实际上已经在流程中创建了第一个大数据集群。本章节将从您创建其余的更多个大数据集群时给您参考。

登陆USDP控制台，点击顶部 <kbd>当前集群</kbd> 的集群名称，会弹出下拉列表，在列表中选择 <kbd>添加集群</kbd> 按钮。如下图所示：

![](../../images/1.0.x/webconsole/clusters/2020123035003.png)


此时，即可进入创建新集群的向导流程中，接下来，您可参考 [通过USDP创建第一个大数据集群](usdp_community/1.0.x/webconsole/cluster_create?id=通过向导方式创建大数据集群) 章节，流程一致，此处就不在赘述了。



## 2. 集群切换

如下图所示，点击顶部 <kbd>当前集群</kbd> 的集群名称，会弹出下拉列表，并在其中点击已经创建的各个“集群名称”选项即可切换至该集群的管理控制台中。如下图所示：

![](../../images/1.0.x/webconsole/clusters/2021012665254.png)

后续针对该集群的管理和使用，可参考如下章节进行操作：