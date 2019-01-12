---
layout: post
title: "[Makefile] 基础--跨目录"
date: 2016-09-18
author: mdgsf
comments: true
categories: c
tags: ubuntu, Makefile
description: ""
published: true #default true
---


### Makefile

```
CROSS=
CROSSCFLAGS=
PLATFORM=

ifdef CROSS
endif


CC=$(CROSS)gcc
CXX=$(CROSS)g++
AR=$(CROSS)ar
STRIP = $(CROSS)strip -R .comment
DYLIB = so
MAKE = make

CFLAGS = -Wall $(CROSSCFLAGS) -fsigned-char
CXXFLAGS = -Wall $(CROSSCFLAGS) -fsigned-char
COMMONFLAGS = -Wall $(CROSSCFLAGS) -fsigned-char
ARFLAGS = rs
LINKFLAGS=
DYLINKFLAGS = -Wl,--retain-symbols-file=ExportedFunctions -fPIC --share 
DEBUGFLAGS = $(COMMONFLAGS) -g -D_DEBUG -Werror 
RELEASEFLAGS = $(COMMONFLAGS) -O2 -fno-strict-aliasing
UTFLAGS = -Dprivate=public -Dprotected=public
COVERAGE = -fprofile-arcs -ftest-coverage


.PHONY: all
all : release

TARGET=main
FLAGS=

ROOTDIR = ../../../libs
APR_LIB = -L$(ROOTDIR)/apr/apr-1.5.2/ubuntu/x86/lib -lapr-1
APR_INC = -I$(ROOTDIR)/apr/apr-1.5.2/ubuntu/x86/include/apr-1
CRYPTO_LIB = -L$(ROOTDIR)/cryptoPlusPlus/cryptoPlusPlus_5.6.3/ubuntu/x86/lib -lcryptopp
CRYPTO_INC = -I$(ROOTDIR)/cryptoPlusPlus/cryptoPlusPlus_5.6.3/ubuntu/x86/include


CURRENT_PATH = .
PUBLIC_PATH = ../../public
CFILES = $(wildcard *.c) $(wildcard $(PUBLIC_PATH)/*.c) 
CXXFILES = $(wildcard *.cpp) $(wildcard $(PUBLIC_PATH)/*.cpp)
CSOURCES = $(notdir $(CFILES))
CXXSOURCES = $(notdir $(CXXFILES))
SOURCES = $(CSOURCES) $(CXXSOURCES)
OBJS = $(CSOURCES:.c=.o) $(CXXSOURCES:.cpp=.o)

VPATH = ../../public

INCS = \
       -I$(PUBLIC_PATH) \
       $(APR_INC) \
       $(CRYPTO_INC)

LIBS = \
       $(APR_LIB) \
       $(CRYPTO_LIB)

%.o : %.c
    $(CC) -c $(FLAGS) $(INCS) $< -o $@

%.o : %.cpp
    $(CXX) -c $(FLAGS) $(INCS) $< -o $@

$(TARGET): $(OBJS)
    $(CXX) $(FLAGS) -o $@ $(OBJS) $(LIBS)


.PHONY: debug
debug: FLAGS=$(DEBUGFLAGS)
debug: clean $(TARGET)

.PHONY: release
release: FLAGS=$(RELEASEFLAGS)
release: clean $(TARGET)

.PHONY: clean
clean:
    rm -rf *.o $(TARGET)

.PHONY: output
output:
    echo $(SOURCES)
    echo $(OBJS)
```



