---
layout: post
title: PyTorch中模型的可复现性
---

https://zhuanlan.zhihu.com/p/109166845


需要添加如下设置:

seed = 2020
torch.manual_seed(seed)
torch.cuda.manual_seed(seed)
torch.cuda.manual_seed_all(seed) # if you are using multi-GPU.
np.random.seed(seed) # Numpy module.
random.seed(seed) # Python random module.

torch.backends.cudnn.benchmark = False       # 默认是True
torch.backends.cudnn.deterministic = True    # 默认是False, 按照pytorch源码这个设为了True, cudnn.benchmark可以不设置



实际测试之后, 单gpu在相同机器不同gpu(但型号相同)上是可以实现模型复现, 但多gpu即使是相同机器相同的gpu仍然不能复现.

此外, cpu或者gpu型号不同的机子上,模型的结果也会不一样.

神奇的是,相同机器上相同单gpu,但该机器根目录/满了的情况下,模型结果也会不一样.