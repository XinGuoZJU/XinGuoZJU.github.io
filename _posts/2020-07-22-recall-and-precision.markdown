---
layout: post
title: False Positive 和 False Negative 含义记忆法
---

### 1. FP, FN, TP, TN
False Positive : False(检测模型不能成功地) Positive (判定出结果是Positive的)

False Negative : False(检测模型不能成功地) Negative (判定出结果是Negative的) 

True Positive : True(检测模型成功地) Positive (判定出结果是Positive的) 

True Negative : True(检测模型成功地) Negative (判定出结果是Negative的) 

具体例子:

病者癌症为恶性，检测结果为良性，则为False Positive，假阳性

病者癌症为良性，检测结果为良性，则为True Positive，真阳性

病者癌症为恶性，检测结果为恶性，则为True Negative，真阴性

病者癌症为良性，检测结果为恶性，则为False Negative，假阴性

或者说因为：

positive/negative（预测结果）：预测、判断为正/负

false/true（实际结果）：（通过真实的结果来得出）预测、判断为真/假。

所以：

False Positive (简称FP)：判断为正，但是判断错了。（实际为负）

False Negative (简称FN)：判断为负，但是判断错了。（实际为正）

True Positive (简称TP)：判断为正，且实际为正。

True Negative (简称TN)：判断未负，且实际为负。

### 2. Recall and Precision

#### precision：精确率（准确率）（查准率）
在**判断为真（TP+FP）**的样例中，**实际为真（TP）**样例的比例。

precision = TP/(TP+FP) * 100%

#### recall：召回率（查全率）
在**实际为真（TP+FN）的样例中，被判断为真（TP）**样例的比例。

recall = TP/(TP+FN) * 100%

#### false-positive rate：误检率
在**所有负/虚假（TN+FP）的样例中，被误识别为真（FP）**样例的比例。

false-positive rate = FP/(TN+FP) * 100%


