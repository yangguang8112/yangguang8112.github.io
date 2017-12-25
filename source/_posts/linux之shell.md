---
title: linux之shell
date: 2016-08-15 19:01:25
tags: 算法/编程
---

本页一定程度上参考了[Vivek Gite及其他贡献者](https://bash.cyberciti.biz/guide/Main_Page)
<!--more-->
### Shell变量及环境 
#### 变量赋值与取用 
``` bash
var1_Name=varValue   # 将varValue赋值给叫var1_Name的变量，变量名称只能有字母，下划线和数字组成，且不能以数字开头。 变量名是区分大小写的
A=1234               # 1234赋给A， , 等号两边不能有空格
var1=NovoTRedia

echo $var1_Name      # $符号接变量名就能调出值
echo $var1+Name      # $后边直到非变量名允许的字符为止，都会被当作变量名
echo ${var1}_Name    # 使用花括号限定变量名的范围

Whome=/TJPROJ1/RNA/yangguang
ls $Whome

goWhome="cd $Whome"
$goWhome
```

#### 定义不可变的变量 
``` bash
readonly Whome=/TJPROJ1/RNA/yangguang   #使用readonly
echo $Whome

Whome=somethingelse   #一旦定义，则不再能更改，整个脚本中都一样

```


#### declare 
``` bash

```

#### 算式展开 
``` bash
echo $((1+5))           # $(())的用法

echo $((x++))
echo $((x++))
echo $((x++))
echo $((x++))           # 看一下每次的值的变化，这是++的用法

echo $((x--))
echo $((x--))
echo $x

echo $((10%3))     #取模

echo $((10**3))     #指数

echo $((10/3))     #除法，整除

echo $((10 - 3))  # 减法

echo $((10 * 3))  # 乘法

echo $((10 >= 9)) #比较， >=, >, <, <=, ==, !=, 

echo $((10 &&  3))  #逻辑运算， AND， 逻辑值分为0和非零

echo $((10 &&  -3))

echo $((10 &&  0))

echo $((10 ||  3))  #逻辑运算， OR

echo $((10 || -3))

echo $((10 ||  0)) 

```




#### 环境 

``` bash

```

### 条件执行 
#### bash语言结构
##### 布尔值
| example | return | True/False |
|--|--|--|
| echo $( ( 5 > 12 ) ) | 0 | False |
| echo $( ( 5 == 10 ) ) | 0 | False |
| echo $( ( 5 != 2 ) ) | 1 | True |
在shell里，0表示false，1或者非零值表示true
#### test命令
语法：test condition
``` bash
#test命令返回布尔值
test 5 -eq 5 && echo Yes || echo No   #  &&与(AND),执行命令1后才会继续执行命令2
test 5 -eq 15 && echo Yes || echo No   #  ||或(OR),执行命令1不成功则执行命令2
test 5 -ne 10 && echo Yes || echo No
test -f /etc/resolv.conf && echo "File /etc/resolv.conf found." || echo "File /etc/resolv.conf not found."
test -f /etc/resolv1.conf && echo "File /etc/resolv1.conf found." || echo "File /etc/resolv1.conf not found."
```
#### if结构
语法：
``` bash
if condition
then
     command1 
     command2
     ...
     commandN 
fi
```
``` bash
#!/bin/bash
read -p "Enter a password" pass
if test "$pass" == "novo2017"
then
     echo "Password verified."
fi
```
#### If..else..fi
``` bash
##!/bin/bash
read -p "Enter a password" pass
if test "$pass" = "novo2017"
then
     echo "Password verified."
else
     echo "Access denied."	
fi
```
#### if..elif..fi
``` bash
#!/bin/bash
read -p "Enter a number : " n
if [ $n -gt 0 ]; then
  echo "$n is a positive."
elif [ $n -lt 0 ]
then
  echo "$n is a negative."
elif [ $n -eq 0 ]
then
  echo "$n is zero number."
else
  echo "Oops! $n is not a number."
fi
```
#### 命令的结束状态
``` bash
date	#执行一个命令
echo $?	#打印该命令结束后的状态，这里会输出0
date2017	#执行一个不存在的命令
echo $?	#打印该命令结束后的状态，这里会输出
ls no_this_dir	#
echo $?	#打印该命令结束后的状态，这里会输出
```
``` bash
#!/bin/bash
# set var 
PASSWD_FILE=/etc/passwd

# 获得用户名
read -p "Enter a user name : " username

grep "^$username" $PASSWD_FILE > /dev/null

# 保存grep命令的结束状态
# 如果找到该用户名则返回0
# 如果没找到则返回非零
status=$?

if test $status -eq 0
then
	echo "User '$username' found in $PASSWD_FILE file."
else
	echo "User '$username' not found in $PASSWD_FILE file."
fi
```
#### 数值比较
``` bash
num1 -eq num2	#num1等于num2
num1 -ge num2 	#num1大于或等于num2
num1 -gt num2 	#num1大于num2
num1 -le num2 	#num1小于或等于num2
num1 -lt num2 	#num1小于num2
num1 -ne num2 	#num1不等于num2
```
#### 字符串比较
``` bash
STRING1 = STRING2	#两字符串相等
STRING1 != STRING2 	#两字符串不相等
test -z STRING 	#字符串长度为0
```
``` bash
#!/bin/bash
read -s -p "Enter your password " pass
echo 
if test -z $pass 
then
    echo "No password was entered!!! Cannot verify an empty password!!!"	
    exit 1
fi
if test "$pass" != "tom"
then
    echo "Wrong password!"
fi
```
#### 文件属性比较
##### -d dir
如果文件存在且为目录则为true
``` bash
#!/bin/bash
DEST="/backup"
SRC="/home"

# 确定要备份的目录存在
[ ! -d "$DEST" ] && mkdir -p "$DEST"

# 如果原目录不存在则退出...
[ ! -d "$SRC" ] && { echo "$SRC directory not found. Cannot make backup to $DEST"; exit 1; }

# 利用打包压缩进行备份
echo "Backup directory $DEST..."
echo "Source directory $SRC..."
tar -zcf "$DEST/backup.tar.gz" "$SRC" 2>/dev/null


# 通过命令执行结束状态将备份状态打印在屏幕上
[ $? -eq 0 ] && echo "Backup done!" || echo "Backup failed"
```
##### -f file
如果file存在且是一个常规文件则为true
``` bash
[ ! -f /path/to/file ] && echo "File not found!"
# ! 非(NO)
```
#### 如何传参
``` bash
#!/bin/bash
echo "这个是脚本的名字 : $0"
echo "这是第一个参数 : $1"
echo "这是第二个参数 : $2"
echo "这是第三个参数 : $3"
echo "这是传参的个数 : $#"
echo "所有的参数 (\$* version) : $*"
echo "所有的参数 (\$@ version) : $@"
```
##### The Difference Between $@ and $*
``` bash
```

### 循环 
#### for循环
语法：
``` bash
for var in item1 item2 ... itemN
    do
        command1
        command2
        ....
        ...
        commandN
    done
#c语言形式
for (( EXP1; EXP2; EXP3 ))#EXP1 初始化，EXP2 条件判断，EXP3 计算表达式
do
	command1
	command2
	command3
done
```
``` bash
#!/bin/bash
for i in 1 2 3 4 5
do
  echo "Welcome $i times."
done
```
##### 使用字符串的for循环
``` bash
#!/bin/bash
for car in bmw ford toyota nissan
   do
   echo "Value of car is: $car"
done
```
##### 使用变量的for循环
``` bash
#!/bin/bash
files="/etc/passwd /etc/group /etc/shadow /etc/gshdow"
for f in $files
do
	[  -f $f ] && echo "$f file found" || echo "*** Error - $f file missing."
done
```
##### 使用命令行结果的for循环
``` bash
#!/bin/bash
echo "Printing file names in /tmp directory:"
for f in $(ls /tmp/*)
do
	echo $f
done
```
#### while循环
语法：
``` bash
 while [ condition ]
    do
    	command1
    	command2
    	..
    	....
    	commandN
    done
```
``` bash
#!/bin/bash
# n赋值1
n=1

# 直到$n等于5，循环结束
while [ $n -le 5 ]
do
	echo "Welcome $n times."
	n=$(( n+1 ))	 # $n递增
done
```
##### 读取一个文件
``` bash
#!/bin/bash
file=/etc/resolv.conf
while read -r line
do
        # file中的行已经赋给$line
	echo $line
done < "$file"
```
#### until循环
#### select循环
#### break语句退出循环
``` bash
#!/bin/bash
match=$1  # fileName
found=0   # set to 1 if file found in the for loop

# show usage
[ $# -eq 0 ] && { echo "Usage: $0 fileName"; exit 1; }

# Try to find file in /etc
for f in /etc/*
do

	if [ $f == "$match" ]
	then
	 	echo "$match file found!"
	 	found=1 # file found
		break   # break the for looop
	fi
done

# noop file not found
[ $found -ne 1 ] && echo "$match file not found in /etc directory"
```
#### continue继续
``` bash
while true
do
	[ condition1 ] && continue
	cmd1
	cmd2
	[ condition2 ] && break
done
```


### 函数 


### 一些专题 



### 脚本 









