---
layout: post
title: Pytorch DistributedDataParallel 踩坑
---

DDP的工作机制是每个gpu单独计算loss和梯度，然后梯度共享，每个gpu获得所有梯度之后分别backpropagate，因此每个gpu上的模型参数才完全一致

可以参考我改的实验代码： https://github.com/XinGuoZJU/ddp_examples


batch_size:		
	1）loss 需要用mean，否则用sum会出现不等价问题： https://pytorch.org/docs/stable/generated/torch.nn.parallel.DistributedDataParallel.html#torch.nn.parallel.DistributedDataParallel  （第五个NOTE）
	2) DDP和DP不同，DDP指定的batch size为B，则每个gpu上用的batch size为B，总的batch size为2B
					DP指定的batch size为B，则每个gpu上用的batch size为B/2，总的batch size为B

sampler:
	1）使用sampler的话则每个epoch会被均分到每个gpu（每个gpu数据和loss不同），不使用的话每个gpu上单独将每个epoch均分（每个gpu数据和loss相同）
	2）set_epoch： sampler的随机化种子是由epoch决定的：https://github.com/pytorch/pytorch/blob/master/torch/utils/data/distributed.py （第100行）因此每个epoch结束，要使用set_epoch方法。

model：
	1）model定义中forward函数的每个输出都必须在loss计算中使用到，否则DDP无法监视训练进程是否同步
	2）DistributedDataParallel 中要加上find_unused_parameters=True


print：
	不做处理的话，每个线程都会print一遍结果，因此一般用torch.distributed.get_rank()获取到是主进程的时候输出

loss和accuracy聚合：
	使用torch.distributed.reduce()对不同进程的loss和accuracy进行聚合