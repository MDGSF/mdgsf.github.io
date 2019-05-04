---
layout: post
title:  "[Windows] 程序自动获取管理员权限"
date:   2016-03-24
comments: true
categories: Windows
tags: [Uac]
description:
published: true
---

## UAC.manifest 文件

把这个文件放在工程目录下

```
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<assembly xmlns="urn:schemas-microsoft-com:asm.v1" manifestVersion="1.0">
    <assemblyIdentity
        version="1.0.0.0"
        processorArchitecture="x86"
        name="Microsoft.Winweb.EthernetDirect"
        type="win32"
    />
    <trustInfo xmlns="urn:schemas-microsoft-com:asm.v3">
        <security>
          <requestedPrivileges>
            <requestedExecutionLevel level='requireAdministrator' uiAccess='false' />
          </requestedPrivileges>
        </security>
    </trustInfo>
</assembly>
```

<img src="{{ site.url }}/images/201603/24_01.png" alt="24_01" />

<img src="{{ site.url }}/images/201603/24_02.png" alt="24_02" />
