---
layout: post
title:  "[python] pycharm 使用"
date:   2019-03-28
comments: true
categories: python
tags: [Python,pycharm]
description:
published: true
---

### 格式化代码

```
菜单栏 ---> Code ---> Reformat Code

快捷键: Ctrl + Alt + L
```

### 查看函数申明

把鼠标光标移动到要查看的函数上

```
右键 ---> Go To ---> Declaration

快捷键： Ctrl + B
```

### 设置模板

File ---> Settings ---> Editor ---> File and Code Templates

然后在右边的选项卡中选择 **Files**

然后选择下面的 **Python Script**

然后把下面代码复制到右边的编辑框中

```python
#!/usr/bin/env python
# -*- coding: UTF-8 -*-


def main():
    pass


if __name__ == "__main__":
    main()
```

最后确定即可。

如何使用呢？

右键 ---> New ---> Python File

然后生成的 python 文件，就会用上面的那个模板填充。


