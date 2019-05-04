---
layout: post
title:  "[python] Scrapy"
date:   2017-08-29
comments: true
categories: python
tags: [Python,Scrapy]
description:
published: true
---

## 1

创建目录

```
E:\ScrapyLego>
```

## 2

创建一个独立的Python运行环境，命名为venv：

```
E:\ScrapyLego>virtualenv --no-site-packages venv
Using base prefix 'c:\\python34'
New python executable in E:\ScrapyLego\venv\Scripts\python.exe
Installing setuptools, pip, wheel...done.
```

命令virtualenv就可以创建一个独立的Python运行环境，我们还加上了参数--no-site-packages，这样，已经安装到系统Python环境中的所有第三方包都不会复制过来，这样，我们就得到了一个不带任何第三方包的“干净”的Python运行环境。

## 3

进入该环境：

```
E:\ScrapyLego\venv\Scripts>activate.bat
(venv) E:\ScrapyLego\venv\Scripts>
```

## 4

安装lxml

<a href="http://www.lfd.uci.edu/~gohlke/pythonlibs/" target="_blank">http://www.lfd.uci.edu/~gohlke/pythonlibs/</a>

```
(venv) E:\ScrapyLego>pip install lxml-3.7.3-cp34-cp34m-win_amd64.whl
Processing e:\scrapylego\lxml-3.7.3-cp34-cp34m-win_amd64.whl
Installing collected packages: lxml
Successfully installed lxml-3.7.3
```

lxml-3.7.3-cp34-cp34m-win_amd64.whl 就是lxml的安装包

**cp34**: python的版本是3.4

## 5

安装scrapy

<a href="https://docs.scrapy.org/en/latest/intro/install.html" target="_blank">https://docs.scrapy.org/en/latest/intro/install.html</a>

```
(venv) E:\ScrapyLego>pip install scrapy
```

## 6

新建文件scraper.py

```python

import scrapy

class BrickSetSpider(scrapy.Spider):
    name = "brickset_spider"
    start_urls = ['http://brickset.com/sets/year-2016']

    def saveHtml(self, response):
        filename = 'sets2016.html'
        with open(filename, 'wb') as f:
            f.write(response.body)

    def parse(self, response):
        self.saveHtml(response)
        SET_SELECTOR = '.set'
        for brickset in response.css(SET_SELECTOR):
            NAME_SELECTOR = 'h1 a ::text'
            yield {
                #'name': brickset.css(NAME_SELECTOR).extract_first(),
                'name': brickset.css(NAME_SELECTOR).extract(),
            }
```

```
(venv) E:\ScrapyLego>scrapy runspider scraper.py
```

**Tips**: ImportError: No module named 'win32api'

```
(venv) E:\ScrapyLego>pip install pypiwin32
Collecting pypiwin32
  Using cached pypiwin32-219-cp34-none-win_amd64.whl
Installing collected packages: pypiwin32
Successfully installed pypiwin32-219
```

```python

import scrapy

class BrickSetSpider(scrapy.Spider):
    name = "brickset_spider"
    start_urls = ['http://brickset.com/sets/year-2016']

    def saveHtml(self, response):
        filename = 'sets2016.html'
        with open(filename, 'wb') as f:
            f.write(response.body)

    def parse(self, response):
        self.saveHtml(response)
        SET_SELECTOR = '.set'
        for brickset in response.css(SET_SELECTOR):
            NAME_SELECTOR = 'h1 a ::text'
            PIECES_SELECTOR = './/dl[dt/text() = "Pieces"]/dd/a/text()'
            MINIFIGS_SELECTOR = './/dl[dt/text() = "Minifigs"]/dd[2]/a/text()'
            IMAGE_SELECTOR = 'img ::attr(src)'
            yield {
                #'name': brickset.css(NAME_SELECTOR).extract_first(),
                'name': brickset.css(NAME_SELECTOR).extract(),
                'pieces': brickset.xpath(PIECES_SELECTOR).extract_first(),
                'minifigs': brickset.xpath(MINIFIGS_SELECTOR).extract_first(),
                'image': brickset.css(IMAGE_SELECTOR).extract_first(),
            }

        NEXT_PAGE_SELECTOR = '.next a ::attr(href)'
        next_page = response.css(NEXT_PAGE_SELECTOR).extract_first()
        if next_page:
            yield scrapy.Request(
                response.urljoin(next_page),
                callback = self.parse
            )

```

<a href="https://www.liaoxuefeng.com/wiki/0014316089557264a6b348958f449949df42a6d3a2e542c000/001432712108300322c61f256c74803b43bfd65c6f8d0d0000" target="_blank">https://www.liaoxuefeng.com/wiki/0014316089557264a6b348958f449949df42a6d3a2e542c000/001432712108300322c61f256c74803b43bfd65c6f8d0d0000</a>

<a href="https://github.com/scrapy/scrapy" target="_blank">https://github.com/scrapy/scrapy</a>

<a href="https://www.digitalocean.com/community/tutorials/how-to-crawl-a-web-page-with-scrapy-and-python-3" target="_blank">https://www.digitalocean.com/community/tutorials/how-to-crawl-a-web-page-with-scrapy-and-python-3</a>
