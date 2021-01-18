---
title: golang命令集
date: '2020-10-23'
spoiler: golang
---
## go文件转成汇编

`go tool compile -N -l -S xx.go`

## go test

1. `go help test`
2. `go help testflag`

## go tool trace
> 跟踪程序的执行情况，包括GC、锁等

1. `go run main.go 2>trace.out`：运行trace到输出文件
2. `go tool trace trace.out`

## 资料整理
1. [go-tool-trace](https://eddycjy.gitbook.io/golang/di-9-ke-gong-ju/go-tool-trace)

