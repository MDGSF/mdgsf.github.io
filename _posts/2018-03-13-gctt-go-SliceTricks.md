---
layout: post
title:  "[GCTT - M] SliceTricks"
date:   2018-03-13
comments: true
categories: GCTT
tags: [Go slice]
description:
published: true
---

# 切片技巧

自从引入了内置的 `append` 后，`container/vector` 包（在 Go 1 中已经被移除）的大部分功能都可以使用 `append` 和 `copy` 来代替了。

这里是 vector 方法和它们的类似切片的操作：

**AppendVector**

```go
a = append(a, b...)
```

**拷贝**

```go
b = make([]T, len(a))
copy(b, a)
// or
b = append([]T(nil), a...)
```

**剪切**

```go
a = append(a[:i], a[j:]...)
```

**删除**

```go
a = append(a[:i], a[i+1:]...)
// or
a = a[:i+copy(a[i:], a[i+1:])]
```

**删除而不保留顺序**

```go
a[i] = a[len(a)-1]
a = a[:len(a)-1]
```

**注意**如果元素的类型是一个指针或一个带有指针字段的结构体，这些指针字段需要被垃圾回收，上述 `Cut` 和 `Delete` 的实现有一个潜在的内存泄漏问题：一些带有值的元素仍然被切片 `a` 引用，因此不能被垃圾回收。以下代码可以解决此问题：

**剪切**

```go
copy(a[i:], a[j:])
for k, n := len(a)-j+i, len(a); k < n; k++ {
	a[k] = nil // or the zero value of T
}
a = a[:len(a)-j+i]
```

**删除**

```go
copy(a[i:], a[i+1:])
a[len(a)-1] = nil // or the zero value of T
a = a[:len(a)-1]
```

**删除而不保留顺序**

```go
a[i] = a[len(a)-1]
a[len(a)-1] = nil
a = a[:len(a)-1]
```

**Expand**

```go
a = append(a[:i], append(make([]T, j), a[i:]...)...)
```

**Extend**

```go
a = append(a, make([]T, j)...)
```

**插入**

```go
a = append(a[:i], append([]T{x}, a[i:]...)...)
```

**注：**第二个 append 创建一个带有其自己的底层存储的新切片，并将 `a[i:]` 中的元素复制到该切片，然后将这些元素复制回切片 `a`（通过第一个 `append`）。通过使用另一种方式可以避免创建新的切片（从而产生内存垃圾）和两次拷贝：

**插入**

```go
s = append(s, 0)
copy(s[i+1:], s[i:])
s[i] = x
```

**InsertVector**

```go
a = append(a[:i], append(b, a[i:]...)...)
```

**Pop/Shift**

```go
x, a = a[0], a[1:]
```

**Pop Back**

```go
x, a = a[len(a)-1], a[:len(a)-1]
```

**Push**

```go
a = append(a, x)
```

**Push Front/Unshift**

```go
a = append([]T{x}, a...)
```

# 其他技巧

**过滤时不需要而外分配空间**

这个技巧基于一个事实：即过滤切片与原始切片共享相同的底层数组和容量，因此该底层数组将被重用于过滤切片。当然，原始内容已被修改。

```go
b := a[:0]
for _, x := range a {
	if f(x) {
		b = append(b, x)
	}
}
```

**反转数组**
 
用相同的元素替换切片的内容，但以相反的顺序替换：

```go
for i := len(a)/2-1; i >= 0; i-- {
	opp := len(a)-1-i
	a[i], a[opp] = a[opp], a[i]
}
```

同样的事情，除了用了两个索引：

```go
for left, right := 0, len(a)-1; left < right; left, right = left+1, right-1 {
	a[left], a[right] = a[right], a[left]
}
```

**洗牌算法**

Fisher–Yates 算法

```go
for i := len(a) - 1; i > 0; i-- {
	j := rand.Intn(i + 1)
	a[i], a[j] = a[j], a[i]
}
```

----------------

via: https://github.com/golang/go/wiki/SliceTricks

