---
layout: post
title:  "[Linux] 查看进程占用的物理内存"
date:   2019-08-27
comments: true
categories: Linux
tags: [proc, vmrss]
description:
published: true
---

```
$ cat /proc/[pid]/status| grep Vm
VmPeak:   704480 kB
VmSize:   647140 kB
VmLck:         0 kB
VmPin:         0 kB
VmHWM:     13192 kB
VmRSS:     13192 kB
VmData:   235720 kB
VmStk:       140 kB
VmExe:     10556 kB
VmLib:      2056 kB
VmPTE:       208 kB
VmSwap:        0 kB
```

```
man proc | vim -
/\/proc\/\[pid\]\/status

* VmPeak: Peak virtual memory size.
* VmSize: Virtual memory size.
* VmLck: Locked memory size (see mlock(3)).
* VmPin: Pinned memory size (since Linux 3.2).  These are pages that can't be moved because something needs to directly access physical memory.
* VmHWM: Peak resident set size ("high water mark").
* VmRSS: Resident set size.
* VmData, VmStk, VmExe: Size of data, stack, and text segments.
* VmLib: Shared library code size.
* VmPTE: Page table entries size (since Linux 2.6.10).
* VmPMD: Size of second-level page tables (since Linux 4.0).
* VmSwap: Swapped-out virtual memory size by anonymous private pages; shmem swap usage is not included (since Linux 2.6.34).
```

VmRss 就是实际占用的物理内存大小。

### Resident set size

[Resident set size](https://en.wikipedia.org/wiki/Resident_set_size)

In computing, **resident set size (RSS)** is the portion of memory occupied by a [process](https://en.wikipedia.org/wiki/Process_(computing)) that is held in [main memory](https://en.wikipedia.org/wiki/Computer_data_storage#Primary_storage) (RAM). The rest of the occupied memory exists in the [swap space](https://en.wikipedia.org/wiki/Paging) or [file system](https://en.wikipedia.org/wiki/File_system), either because some parts of the occupied memory were paged out, or because some parts of the executable were never loaded.

