---
layout: post
title:  "[Linux] nvidia-smi 命令"
date:   2018-12-06
comments: true
categories: Linux
tags: [Linux,Nvidia-smi]
description:
published: true
---

`nvidia-smi` 查看 GPU 数据

`watch -d -n 1 nvidia-smi` 实时监控 GPU 数据

```
➜  huangjian nvidia-smi
Thu Dec  6 11:41:49 2018
+-----------------------------------------------------------------------------+
| NVIDIA-SMI 390.46                 Driver Version: 390.46                    |
|-------------------------------+----------------------+----------------------+
| GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
| Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
|===============================+======================+======================|
|   0  Tesla P100-PCIE...  On   | 00000000:00:09.0 Off |                    0 |
| N/A   32C    P0    32W / 250W |   6050MiB / 16280MiB |      0%      Default |
+-------------------------------+----------------------+----------------------+

+-----------------------------------------------------------------------------+
| Processes:                                                       GPU Memory |
|  GPU       PID   Type   Process name                             Usage      |
|=============================================================================|
|    0     32592      C   python                                      1067MiB |
|    0     32668      C   /mnt/disk2/anaconda3/bin/python             4965MiB |
+-----------------------------------------------------------------------------+
```

上面的表格中： 

第一栏的Fan：N/A是风扇转速，从0到100%之间变动。有的设备不会返回转速，因为它不依赖风扇冷却而是通过其他外设保持低温。 

第二栏的Temp：是温度，单位摄氏度。 

第三栏的Perf：是性能状态，从P0到P12，P0表示最大性能，P12表示状态最小性能。 

第四栏下方的Pwr：是能耗，上方的Persistence-M：是持续模式的状态，持续模式虽然耗能大，但是在新的GPU应用启动时，花费的时间更少，这里显示的是on的状态。 

第五栏的Bus-Id是涉及GPU总线的东西，domain:bus:device.function 

第六栏的Disp.A是Display Active，表示GPU的显示是否初始化。 

第五第六栏下方的Memory Usage是显存使用率。 

第七栏是浮动的GPU利用率。 

第八栏上方是关于ECC的东西。 

第八栏下方Compute M是计算模式。 

下面一张表示每个进程占用的显存使用率。
