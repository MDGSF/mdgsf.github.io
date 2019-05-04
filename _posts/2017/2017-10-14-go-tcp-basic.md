---
layout: post
title:  "[GO] tcp client server简单实现"
date:   2017-10-14
comments: true
categories: GO
tags: [GO]
description: ""
published: true
---

## client

```go
package main

import (
    "bufio"
    "fmt"
    "jian/tcp/tools"
    "net"
    "os"
    "strings"
)

func main() {
    conn, err := net.Dial("tcp", "localhost:3333")
    if err != nil {
        fmt.Println("Error dialing", err.Error())
        return
    }

    inputReader := bufio.NewReader(os.Stdin)
    for {
        input, _ := inputReader.ReadString('\n')
        trimmedInput := strings.Trim(input, "\r\n")
        if trimmedInput == "Q" {
            return
        }

        b, err := tools.PackToJSON(1, []byte(trimmedInput))
        if err != nil {
            return
        }

        tools.Write(conn, string(b))
    }
}
```

## server

```go
package main

import (
    "fmt"
    "jian/tcp/tools"
    "net"
    "os"
)

const (
    CONN_HOST = "localhost"
    CONN_PORT = "3333"
    CONN_TYPE = "tcp"
    BUF_SIZE  = 4 * 1024
)

func main() {
    l, err := net.Listen(CONN_TYPE, CONN_HOST+":"+CONN_PORT)
    if err != nil {
        fmt.Println("listen failed:", err.Error())
        os.Exit(1)
    }
    defer l.Close()
    fmt.Println("Listening on " + CONN_HOST + ":" + CONN_PORT)

    for {
        conn, err := l.Accept()
        if err != nil {
            fmt.Println("accept failed:", err.Error())
        }

        go handleRequest(conn)
    }
}

func handleRequest(conn net.Conn) {

    defer conn.Close()

    for {
        msg, msgLen, err := tools.Read(conn)
        if err != nil {
            fmt.Println("Read failed.", err.Error())
            return
        }
        fmt.Println("msg and msgLen", msg, msgLen)

        msgtype, data, err := tools.UnpackJSON(msg)
        if err != nil {
            fmt.Println("UnpackJSON failed.", err.Error())
            return
        }
        fmt.Println("msgtype and data", msgtype, data)
    }
}
```

## tools

```go
package tools

import (
    "bytes"
    "encoding/binary"
    "encoding/json"
    "fmt"
    "net"
    "strings"
)

/*
Msg message struct
*/
type Msg struct {
    Msgtype int
    Data    []byte
}

/*
PackToJSON create json string.
*/
func PackToJSON(msgtype int, data []byte) ([]byte, error) {
    msgBag := &Msg{}
    msgBag.Msgtype = msgtype
    msgBag.Data = data

    b, err := json.Marshal(msgBag)
    if err != nil {
        return nil, err
    }
    return b, nil
}

/*
UnpackJSON unpack json string.
*/
func UnpackJSON(msg []byte) (msgtype int, data []byte, err error) {
    msgBag := &Msg{}
    err = json.Unmarshal(msg, &msgBag)
    if err != nil {
        return 0, nil, err
    }
    return msgBag.Msgtype, msgBag.Data, nil
}

/*
IntToBytes int to bytes
*/
func IntToBytes(n int) []byte {
    temp := int32(n)
    bytesBuffer := bytes.NewBuffer([]byte{})
    binary.Write(bytesBuffer, binary.BigEndian, temp)
    return bytesBuffer.Bytes()
}

/*
BytesToInt bytes to int
*/
func BytesToInt(b []byte) int {
    bytesBuffer := bytes.NewBuffer(b)
    var temp int32
    binary.Read(bytesBuffer, binary.BigEndian, &temp)
    return int(temp)
}

/*
Read read msg with header 4 byte.
*/
func Read(conn net.Conn) ([]byte, int, error) {

    //conn.SetReadDeadline(time.Now().Add(1e9))

    headerBuf := make([]byte, 4)
    headerLen, err := conn.Read(headerBuf)
    if err != nil || headerLen != 4 {
        fmt.Println("read header failed:", err.Error(), headerLen)
        return nil, 0, err
    }

    bodyLen := BytesToInt(headerBuf)
    //fmt.Println("read head success, body len:", bodyLen)

    bodyBuf := make([]byte, bodyLen)
    readedbodyLen, err := conn.Read(bodyBuf)
    if err != nil || readedbodyLen != int(bodyLen) {
        fmt.Println("read body failed:", err.Error(), readedbodyLen)
        return nil, 0, err
    }
    //fmt.Println("read body success,", bodyBuf)

    return bodyBuf, bodyLen, nil
}

/*
Write write msg with header 4 byte
*/
func Write(conn net.Conn, msg string) error {

    msgLen := strings.Count(msg, "") - 1

    //fmt.Println(IntToBytes(msgLen))
    _, err := conn.Write(IntToBytes(msgLen))
    if err != nil {
        fmt.Println("Write header failed.", err.Error())
        return err
    }

    //fmt.Println([]byte(msg))
    _, err = conn.Write([]byte(msg))
    if err != nil {
        fmt.Println("Write failed:", err.Error())
        return err
    }

    return nil
}
```
