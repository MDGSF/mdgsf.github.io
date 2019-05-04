---
layout: post
title:  "[GO] 简单的TCP Server实现"
date:   2016-12-26
comments: true
categories: GO
tags: [GO]
description: ""
published: true
---

```go
package main

import (
    "flag"
    "fmt"
    "net"
    "syscall"
)

const maxRead = 25

func main() {
    flag.Parse()
    if flag.NArg() != 2 {
        panic("usage: host port")
    }
    hostAndPort := fmt.Sprintf("%s:%s", flag.Arg(0), flag.Arg(1))
    listener := initServer(hostAndPort)
    for {
        conn, err := listener.Accept()
        checkError(err, "Accept: ")
        go connectionHandler(conn)
    }
}

func initServer(hostAndPort string) *net.TCPListener {
    serverAddr, err := net.ResolveTCPAddr("tcp", hostAndPort)
    checkError(err, "Resolving address:port failed: '"+hostAndPort+"'")
    listener, err := net.ListenTCP("tcp", serverAddr)
    checkError(err, "ListenTCP: ")
    println("Listening to: ", listener.Addr().String())
    return listener
}

func connectionHandler(conn net.Conn) {
    connFrom := conn.RemoteAddr().String()
    println("Connection from: ", connFrom)
    sayHello(conn)
    for {
        var ibuf []byte = make([]byte, maxRead+1)
        length, err := conn.Read(ibuf[0:maxRead])
        ibuf[maxRead] = 0
        switch err {
        case nil:
            handleMsg(length, err, ibuf)
        case syscall.Errno(0xb):
            continue
        default:
            goto DISCONNECT
        }
    }
DISCONNECT:
    err := conn.Close()
    println("Closed connection: ", connFrom)
    checkError(err, "Close: ")
}

func sayHello(to net.Conn) {
    obuf := []byte{'L', 'e', 't', '\'', 's', ' ', 'G', 'o', '!', '\n'}
    wrote, err := to.Write(obuf)
    checkError(err, "Write: wrote "+string(wrote)+" bytes.")
}

func handleMsg(length int, err error, msg []byte) {
    if length > 0 {
        print("<", length, ":")
        for i := 0; ; i++ {
            if msg[i] == 0 {
                break
            }
            fmt.Printf("%c", msg[i])
        }
        print(">\n")
    }
}

func checkError(err error, info string) {
    if err != nil {
        panic("ERROR: " + info + " " + err.Error())
    }
}
```

```go
package main

import (
    "bufio"
    "fmt"
    "net"
    "os"
    "strings"
    "time"
)

func main() {
    conn, err := net.Dial("tcp", "localhost:50000")
    if err != nil {
        fmt.Println("Error dialing", err.Error())
        return
    }

    inputReader := bufio.NewReader(os.Stdin)
    fmt.Println("First, what is your name?")
    clientName, _ := inputReader.ReadString('\n')
    trimmedClient := strings.Trim(clientName, "\n")

    for {
        //fmt.Println("What to send to the server? Type Q to quit.")
        //input, _ := inputReader.ReadString('\n')
        //trimmedInput := strings.Trim(input, "\n")
        //if trimmedInput == "Q" {
        // return
        //}
        //_, err = conn.Write([]byte(trimmedClient + " says: " + trimmedInput))
        _, err = conn.Write([]byte(trimmedClient + " says: " + "Hello"))

        time.Sleep(1000000000)
    }
}
```
