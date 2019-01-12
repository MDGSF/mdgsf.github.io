---
layout: post
title:  "[GO] delete range map"
date:   2017-09-27
comments: true
categories: GO
tags: GO
description: ""
published: true
---

## 1

```go
package main

import (
	"fmt"
)

func main() {
	mymap := make(map[int]string)

	mymap[1] = "test1"
	mymap[2] = "test2"
	mymap[3] = "test3"
	mymap[4] = "test4"

	fmt.Println(mymap)

	for k, v := range mymap {
		fmt.Println(k, v)
	}

	for k := range mymap {
		if k == 1 {
			delete(mymap, k) //如果C++这么写的话，迭代器会出问题。
		}
	}

	fmt.Println(mymap)
}
```

输出:

```
map[4:test4 1:test1 2:test2 3:test3]
1 test1
2 test2
3 test3
4 test4
map[2:test2 3:test3 4:test4]
```

## 2

```go
package main

import "fmt"

func testSlice() {
	a := []int{1, 2, 3, 4, 5}

	fmt.Println(a)
	first := true
	for i, userID := range a {
		fmt.Println(i, userID)
		if first {
			a = a[:2]
		}
		fmt.Println(a)
	}
}

func main() {
	testSlice()
}
```

输出:

```
[1 2 3 4 5]
0 1
[1 2]
1 2
[1 2]
2 3
[1 2]
3 4
[1 2]
4 5
[1 2]
```


## 3

```go
package main

import "fmt"

func testDump() {
	a := []int{2, 2}

	fmt.Println(a)
	for i, userID := range a {
		fmt.Println(i, userID)
		if userID == 2 {
			a = append(a[:i], a[i+1:]...)
		}
		fmt.Println(a)
	}
}

func main() {
	testDump()
}
```

输出:

```
$ go run test.go
[2 2]
0 2
[2]
1 2
panic: runtime error: slice bounds out of range

goroutine 1 [running]:
main.testDump()
        e:/Go/GOPATH/src/jian/test/test.go:12 +0x3ef
main.main()
        e:/Go/GOPATH/src/jian/test/test.go:33 +0x27
exit status 2
```

这种情况下会crash掉，所以在遍历的时候，最好还是不要乱修改。

