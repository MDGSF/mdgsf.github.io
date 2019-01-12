---
layout: post
title:  "[GO] fsnotify监控目录"
date:   2017-10-12
comments: true
categories: GO
tags: GO 
description: ""
published: true
---


```go
package main

import (
    "fmt"
    "log"
    "os"

    "github.com/fsnotify"
)

var watcher *fsnotify.Watcher

func watchDir(path string) {
    fmt.Println("watchDir:", path)
    err := watcher.Add(path)
    if err != nil {
        fmt.Println("watcher.Add failed", err.Error())
        return
    }
}

func isDir(path string) bool {
    fileInfo, err := os.Stat(path)
    if err != nil {
        fmt.Println("os.Stat failed", err.Error())
        return false
    }
    return fileInfo.IsDir()
}

func browserDir(path string) {
    fmt.Println("browserDir:", path)

    dir, err := os.Open(path)
    if err != nil {
        fmt.Println("os.Open failed", err.Error())
        return
    }
    defer dir.Close()

    watchDir(path)

    names, err := dir.Readdirnames(-1)
    if err != nil {
        fmt.Println("dir.Readdirnames failed", err.Error())
        return
    }

    for _, name := range names {
        sub := path + "\\" + name
        if !isDir(sub) {
            continue
        }
        browserDir(sub)
    }
}

func main() {
    var err error
    watcher, err = fsnotify.NewWatcher()
    if err != nil {
        fmt.Println("fsnotify.NewWatcher failed", err.Error())
        return
    }
    defer watcher.Close()

    browserDir("E:\\fsnotify_demo")

    done := make(chan bool)
    go func() {
        for {
            select {
            case event := <-watcher.Events:
                log.Println("event:", event)
                if event.Op&fsnotify.Create == fsnotify.Create {
                    log.Println("Create file:", event.Name)
                }
                if event.Op&fsnotify.Write == fsnotify.Write {
                    log.Println("Write file:", event.Name)
                }
                if event.Op&fsnotify.Remove == fsnotify.Remove {
                    log.Println("Remove file:", event.Name)
                }
                if event.Op&fsnotify.Rename == fsnotify.Rename {
                    log.Println("Rename file:", event.Name)
                }
                if event.Op&fsnotify.Chmod == fsnotify.Chmod {
                    log.Println("Chmod file:", event.Name)
                }
            case err := <-watcher.Errors:
                log.Println("error:", err)
            }
        }
    }()

    <-done
}
```


