---
layout: post
title: "[Makefile] 基础"
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

INCS = \

LIBS = \

OBJS= \
      main.o 


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


#
# -Wall: open some very useful warning options.
# -Werror: treat warning as error.
#
# -fsigned-char: define 'char' as signed char.
# -funsigned-char: define 'char' as unsigned char.
#
# -g: generate debug information.
#
# -Dname: define a macro "name".
# -D_DEBUG: define a macro "_DEBUG".
# -Dprivate=public: define a macro "private", value is "public".
# -Dprotected=public: define a macro "protected", value is "public".
#
# -O0: no optimization.
# -O (-O1): try to optimize.
# -O2: more optimization, but no "trade space for time".
# -O3: more optimization than -O2
# -fstrict-aliasing:
# -fno-strict-aliasing:
#
```
