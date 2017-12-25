---
title: 集群无网下载--except应用
date: 2017-07-28 01:13:53
tags: 小工具
---
集群大部分节点不联外网,自己本地电脑下载,再通过fz上传比较麻烦
此工具利用RNA小服务器上的虚拟机与集群交互,自动下载上传集群
这样不仅可以免去一些机械的人工操作,而且还能避免本地PC下载中断
交互用的就是shell里自带的except,简单粗暴
自动登录在集群上登录内网小服务器,此小服务器可联外网,小服务器上下载完毕,scp上传集群,然后删除小服务器上的文件
<!--more-->
### except脚本
文件 login_RNA_server
``` bash
#!/usr/bin/expect
set url [lindex $argv 0]
set y_date [lindex $argv 1]
set account [lindex $argv 2]
set outdir [lindex $argv 3]
spawn ssh rna@172.25.35.15
expect {
	"*Warning: Permanently added" {exp_continue}
	"*password" {send "123456\r";exp_continue}
	"\\\$" {send "\[ -d $account \] && cd $account || mkdir $account;cd $account\r"}
}
expect "\\\$"
send "\[ -d $y_date \] && cd $y_date || mkdir $y_date;cd $y_date\r"
expect "\\\$"
send "wget $url\r"
expect "\\\$"
send "exit\r"
expect eof

spawn scp rna@172.25.35.15:/home/rna/$account/$y_date/* $outdir
expect {
        "*password:" {send "123456\r";exp_continue}
}

spawn ssh rna@172.25.35.15
expect {
        "*Warning: Permanently added" {exp_continue}
        "*password" {send "123456\r";exp_continue}
        "\\\$" {send "rm -rf /home/rna/$account/$y_date\r"}
}
expect "\\\$"
send "exit\r"
expect eof
```
### shell脚本传参
文件 easy_download
``` bash
#!/bin/bash
account=`whoami`
y_date=`date "+%Y-%m-%d_%H-%M-%S"`
url=$1
usage="easy_download [url] [outdir:default(`pwd`)]"
[ $# -gt 2 ] || [ $# -eq 0 ] && echo $usage && exit
[ $# -eq 2 ] && outdir=$2 || outdir=`pwd`
login_RNA_server $url $y_date $account $outdir
```
### 用法
``` bash
easy_download [url] [outdir:default(`pwd`)]
#[url]为下载文件的链接
#[outdir]为输出路径,默认是当前路径
```