---
layout: post
title:  "[C/C++] writev and readv"
date:   2016-02-27
comments: true
categories: C/C++
tags: [Writev,Readv]
description:
published: true
---

~~~cpp
#include <sys/uio.h>
ssize_t readv(int fd, const struct iovec *iov, int iovcnt);
ssize_t writev(int fd, const struct iovec *iov, int iovcnt);

struct iovec {
    void *iov_base;  /* Starting address */
    size_t iov_len;  /* Number of bytes to transfer */
};
~~~

The readv() system call reads iovcnt buffers from the file associated with the file descriptor fd into the buffers described by iov ("scatter input").

The writev() system call writes iovcnt buffers of data described by iov to the file associated with the file descriptor fd ("gather output").


## Example:
```cpp
#include <unistd.h>
#include <sys/uio.h>
#include <stdio.h>
#include <fcntl.h>

int main(int argc, char *argv[])
{
    ssize_t iSize;
    char acBuf1[9];
    char acBuf2[9];
    struct iovec iov[2];

    int iFd1 = open(argv[1], O_RDONLY);
    int iFd2 = open(argv[2], O_RDONLY);
    int iFd3 = open(argv[3], O_WRONLY);

    iSize = read(iFd1, acBuf1, sizeof(acBuf1));
    iSize = read(iFd2, acBuf2, sizeof(acBuf2));

    iov[0].iov_base = acBuf1;
    iov[0].iov_len = sizeof(acBuf1);
    iov[1].iov_base = acBuf2;
    iov[1].iov_len = sizeof(acBuf2);

    iSize = writev(iFd3, iov, 2);

    close(iFd1);
    close(iFd2);
    close(iFd3);

    return 0;
}
```

建立3个文件（test1， test2， test3）

test1写入12345

test2写入asdfghi

test3为空

运行

./a.out test1 test2 test3
