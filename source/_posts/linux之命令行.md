---
title: lncRNA之命令行
date: 2016-7-28 11:54:08
tags: 算法/编程
---
[Linux系统](https://en.wikipedia.org/wiki/Linux)是一类自由开源的电脑操作系统，因其内核为Linux而得名。内核是一个电脑程序，组成操作系统的核心，链接系统硬件和应用程序。Linux系统是自由和开源的，有很多的[发行版本](https://en.wikipedia.org/wiki/Linux_distribution)。因为Linux免费、开源、稳定性好、安全性高、有活力的社区支持，绝大多数生物信息工具均是基于Linux系统的。
<!--more-->
### 集群 
将多个计算机（节点）连接在一起组成**集群**，我们可以登录到其中某个节点，通过这个节点向其它节点投递任务。


### Linux初体验 
#### 工作目录 
登录之后，可以查看目前所在的目录，一般为为你的 HOME目录，也可能不是，先看看：
``` bash
pwd
```

#### 目录下内容查看 

``` bash
ls       # 查看当前工作目录下的文件和文件夹
ls -l    # 展示更多的信息,分9列，第一列第一个字符如果是d代表文件夹，是-代表是文件，往后的每三个字符为一组，一次代表文件所有者、所有者所在组、组外成员的权限，r代表能读，w代表能写(能编辑和删除)，x代表可执行。第五列展示文件大小，第9列为文件/夹名称
ls /     # 查看 / 下的文件夹
ls /home 

ls -la   # a表示全部，列出隐藏文件及文件夹， .表示目录本身，..表示上一层目录

ls -lha  # h表示大小一栏转为便于人类识别的形式，加上单位。 默认情况下单位为字节。
```

#### 切换目录 

``` bash
PWD=`pwd`   # 这里先将目前的路径存为变量PWD
echo $PWD   # 查看变量的值
pwd  

cd /home    # 切换到 /home
pwd         
ls

cd          # 后边不加参数的时候，默认切换到HOME目录
pwd
ls

cd ~        #~指代用户的HOME目录
pwd
ls


cd ..       # 切换到上一级目录 .. 指上一级目录
ls 

cd $PWD     #切换回原来的目录
```

#### 文件夹和文件操作 
``` bash
mkdir  yangguang # 创建以你的名字为名的文件夹，这里改成的名字
cd yangguang     # 进入刚创建的文件夹
ls              # 什么也没有

mkdir test1/test2    # 报错
mkdir -p  test1/test2    # 这样可以创建test2文件夹并且创建其所在的文件夹

ls

ls test1

touch test1.txt   #touch命令来创建一个新的文件，这个文件是空的

ls 

ls -l              # 留意文件和文件夹第一列第一个字符的区别


cp test1.txt Test1.txt   # 复制文件  Linux是区分大小写的
ls -l
cp test1  Test1          # 报错
cp -r test1  Test1       # 复制文件夹，包括文件夹中的内容
ls -l

mv  Test1.txt  Test2.txt   # 文件重命名
ls -l
mv  Test2.txt  ./test1     #移动文件的位置
ls -l 
ls -l test1

mv Test1  Test2  
ls -l

rm test1.txt       # 删除文件
ls -l
ls -l test1

rm test1/test2     # 报错
rm -r test1/test2  # 删除文件夹
ls -l
ls -l test1

rmdir test1        # 删除文件夹
ls -l

#用如下一行命令先创建一个2列12行的文件
echo -e '1\ta\n2\tb\n3\tc\n4\td\n5\te\n6\tf\n7\tg\n8\th\n9\ti\n10\tj\n11\tk\n12\tl' > test1.tsv 
echo -e '1\tA\n2\tB\n3\tC\n4\tD\n5\tE\n6\tF\n7\tG\n8\tH\n9\tI\n10\tJ\n11\tK\n12\tL' > test2.tsv

cat test1.tsv   #查看文件的所有内容，不要用于大文件，不然可能会很卡

cat test1.tsv > test.tsv # > 为重定向，把输到stdout (屏幕)上的重定向到文件
cat test.tsv
cat test2.tsv > test.tsv #原来文件将被覆盖
cat test.tsv

cat test1.tsv > test.tsv # > 为重定向，把输到stdout (屏幕)上的重定向到文件
cat test.tsv
cat test2.tsv >> test.tsv # >> 为将内容追加到文件末尾
cat test.tsv

cat test1.tsv test2.tsv test1.tsv test2.tsv test1.tsv test2.tsv > test.tsv  #多个文件合并为一个文件

wc -l test.tsv   #产看文件有多少行

less test.tsv  #查看文件，用上下箭头控制； 按q退出

more test.tsv  #用回车键(Enter)往后翻

head test.tsv   #查看前边几行，自己数是几行
head -n 5 test.tsv  # 产看前边5行，可以改成其它的数字

tail test.tsv   #查看末尾几行，自己数是几行
tail -n 5 test.tsv  # 产看末尾5行，可以改成其它的数字

tail -n 20 test.tsv  >  Test.tsv  #重定向
cat Test.tsv
wc -l Test.tsv


l -l
gzip test.tsv       #压缩文件成为 gz格式的
l -l                #留意文件大小的变化

gunzip test.tsv.gz  

tar -czvf test.tar.gz  test1.tsv test2.tsv test.tsv #多个文件压缩到一个压缩包里
less test.tar.gz         #查看压缩包里的内容
tar -xzvf test.tar.gz    #解压缩

```




#### 常用命令 
``` bash
ls --help   # 查看帮助信息

man ls       # 查看手册，信息更全面

cd --help    #不是所有的命令都可以用--hlep查看帮助信息
man cd       #man更好用，信息也会更全面

man man       #man本身也有手册


who      # 查看谁在线
whoami   # 查看自己现在以那个用于登录

date     #查看现在的时间

echo 1   # echo 用来展示变量的值或者值本身，字符等本身就是值
echo q   # 
echo DATE
echo $DATE    # $符号用于取出变量的值，这里DATE这个变量没有定义，所以echo出来的是空的
echo date 
echo `date`   # `` 号之中加命令，能够将命令的结果存为变量
DATE=`date`   #讲date的结果赋值给DATE
echo $DATE
`date`

```

### 文件的权限 
文件具有可写（w）、可读（r）、可执行（x）等权限，通过chmod命令来设置。 
``` bash
echo "Hi RNA" > test.txt  #示例文件
cat test.txt  
ls -l test.txt     # 初始的是 rw-r--r--

chmod =x test.txt  # 将user、group、other的权限都设成x， 体会等号的用法
ls -l test.txt    

chmod u=w test.txt  # 将user的权限都设成w， 体会指定u、g、o的效果
ls -l test.txt 
   
chmod u+r test.txt  # 将user的权限增加r， 体会+的作用
ls -l test.txt    

chmod o-x test.txt  # 将other的权限减少x，体会-的作用
ls -l test.txt    

chmod 111 test.txt  # 将user、group、other的权限都设成x；可以用数字代表权限
ls -l test.txt    
cat test.txt     #不可读了
echo "Hi Hong" > test.txt     #不可写了

chmod 222 test.txt  # 将user、group、other的权限都设成w
ls -l test.txt    
cat test.txt   #不可读
echo "Hi Hong" >> test.txt     #可写


chmod 444 test.txt  # 将user、group、other的权限都设成r
ls -l test.txt    
cat test.txt   #可读
echo "Hi Hong" >> test.txt     #不可写

chmod 731 test.txt  # 将user、group、other的权限分别设成rwx、-wx、--x
ls -l test.txt    

mkdir -p test1/test2
chmod 111 test1
ls -l 
ls -l test1   #没有权限了

chmod 777 test1   #最宽松权限，要慎重使用
ls -l 
ls -l test1   #看看 test1里的文件夹test2并没有跟着变化

chmod -R 775 test1   # -R 实现递归，就是文件夹中的文件已经文件夹都会跟着改变，直到最底层
ls -l 
ls -l test1   #看看 test1里的文件夹test2 跟着变化了

```
1代表x，2代表w，4代表r。是rwx这个数序上的二进制数字的十进制值。 

### 相对路径与绝对路径 
从根目录出发的的绝对路径，比如
``` bash
ls -l /home

pwd  #pwd返回当前目录的绝对路径
```
从当前目录出发的是相对路径，比如
``` bash
ls -l ./test1.txt  
ls -l test1.txt  # ./ 可以省略
ls -l test1/test2  
ls -l ..
ls -l ./../

pwd
cd .           #  . 代表当前工作目录
pwd            
cd ./          #依然在当前目录
pwd
cd ./././././  
pwd            #绝对路径并未改变

cd ..          #.. 表示上一级目录，省略了当前路径
pwd
cd ./..
cd ./../

```



### 硬链接与软连接 
数据在硬盘里的存储单元就是文件，文件都有文件名和数据。数据包括用户数据(user data) + 元数据 (metadata)。 用户数据就是文件的真实内容，元数据为文件的附加属性，文件大小、创建时间、所有者等信息。元数据中的索引节点(inode)号才是文件的唯一标识。为了方便文件的共享，Linux中引入了软连接和硬链接两种，硬链接是指具有相同inode号的文件，而软连接则是指向另一个文件的特殊文件，其特殊性在于，当你对软连接文件进行一些操作时，实际上是对指向的文件进行操作。这样当删掉硬链接中的某一个时，对另一个没有影响，而删除了软连接的源文件之后，这个软连接就成了死链接。 不能对文件夹创建硬链接。

``` bash
rm test1.txt test2.txt test3.txt   # 如果这些文件存在，先删除了，如果不存在，报错也无所谓，
touch test1.txt
stat test1.txt    #stat 命令查看文件的附加属性，包括文件名， inode号等等的。
ls -i test1.txt   # -i 能够同时展示inode号
ls -li test1.txt  # 增加的第一列是inode号

ln test1.txt test2.txt     # 对test1.txt创建一个硬链接叫test2.txt
ls -li                     # inode号同

ln -s test1.txt test3.txt  # 加-s后，对test1.txt创建一个软链接叫test3.txt
ls -li                     # inode号不同

ln -s `pwd`/test1.txt test4.txt
ls -li                     #看一下使用绝对路径的情况

stat test1.txt test2.txt test3.txt test4.txt  # 硬链接除了文件名以外，其它的信息都是相同的，软连接则是一个新的文件

mkdir test1   #如果上文已经创建，这里报错是正常的

mv test2.txt  test1
mv test3.txt  test1
mv test4.txt  test1

cd test1
ls -li       # 可以看到test3.txt变成了死链接，因此制作软连接的时候，最好使用绝对路径
stat ../test1.txt test2.txt test3.txt test4.txt  

rm ../test1.txt
ls -li    #删除源文件之后，软连接变成了死链接
```


### 管道 
很多linux的命令输出直接达到屏幕上，也就是输出到stdout，这些输出可以直接作为一些命令的输入，也就是stdin。 这时候中间用 | 链接，也就是管道。

``` bash
echo -e '1\ta\n2\tb\n3\tc\n4\td\n5\te\n6\tf\n7\tg\n8\th\n9\ti\n10\tj\n11\tk\n12\tl' > test1.tsv 
echo -e '1\tA\n2\tB\n3\tC\n4\tD\n5\tE\n6\tF\n7\tG\n8\tH\n9\tI\n10\tJ\n11\tK\n12\tL' > test2.tsv

cat test1.tsv test2.tsv | less

cat test1.tsv test2.tsv | wc -l    #统计总行数
wc -l test1.tsv test2.tsv 

cat test1.tsv test2.tsv | wc -c

ls -l | less
```

### grep 
grep (**g**lobal **r**egular **e**xpression **p**rinter) 是一个非常常用的命令。逐行对文件进行指定模式的搜索。
``` bash
echo -e 'ACT TAG AGG TAG\nATG TTT\nAAA GGG\nCCC\nATG' > input.txt  #示例文件
cat input.txt

grep ATG input.txt      # 返回包含ATG的行

grep -c ATG input.txt   # 返回包含ATG的行的总行数

grep ^C input.txt       # ^表示开头

grep T$ input.txt       # $表示结尾

ls -l | grep ^d   # 列出所有文件夹
```

### sort 
根据特定的列对文件进行排序.
``` bash
echo -e 'chr1\t1\t100\nchr2\t4\t10\nchr1\t4\t10\nchr3\t3\t40' > test1.bed

sort test1.bed                  # 不分列
sort -k2 test1.bed              # 按照第2列排序
sort -k2n test1.bed             # 按照第2列排序，但当做数字
sort -k2,2n test1.bed           # 这和上一条也是一样的，根据第2列到第2列排序。 -km,n 根据第m到第n列排序
sort -k1 test1.bed              # 按照第1列排序 
sort -k1,1 -k2,2n test1.bed     # 先按第一列排，再按第二列排 
sort -k2,2n -k1,1 test1.bed     # 按照第二排序，再按第一列排

```

### uniq 
uniq总是要接着sort使用，因为它只会对相邻的元素判断是否重复。
``` bash
echo -e 'chr1\nchr2\nchr1\nchr3\nchr4\nchr1\nchr5\nchr2\nchr6\nchr3' > test1.txt
cat test1.txt
uniq test1.txt
sort test1.txt | uniq            # 看看sort之后uniq的效果
cat test1.txt  | uniq      
cat test1.txt  | sort | uniq     
sort test1.txt | uniq -c         # -c统计各元素的重复次数
```


### sed 
sed (**s**tream **ed**itor)用来对输入文件或者数据流进行编辑，逐行进行编辑，sed没有交互模式。通常我们用来做替换。

``` bash
echo -e 'ACT TAG AGG TAG\n TTT\nAAA GGG\nCCC\nATG' > input.txt  #产生一个示例文件
cat input.txt  #查看一下文件

sed '' input.txt  # 不加指令的时候会把每一行输出来

sed -n '' input.txt  # -n 表示安静地进行，告诉它做什么，它才做，这里就是抑制了默认的情况

sed -n 'p' input.txt  # p 表示打印

sed 's/TAG/!STOP!/'  input.txt   # s/x/y/ 表示将x替换成为y

sed 's/TAG/!STOP!/ g'  input.txt  # g 表示全局之后替换所有的

sed 's/T/U/g' input.txt   # 这是一个将DNA序列转成RNA列的例子

sed 's/T/U/ 2' input.txt   # 只对第二次出现的模式进行操作

sed -n 's/T/U/g' input.txt   # -n  

sed -n 's/T/U/gp'  input.txt   # p 

sed -e 's/T/U/g' -e 's/A/a/g'  input.txt  #当想使用多个命令的时候，使用 -e， 每个-e后边跟一个命令，可以有很多

echo -e "s/T/U/g\ns/A/a/g" > edit.sed  #可以把多条命令放在一个文件中作为脚本使用
cat edit.sed   #看看脚本里的内容

sed -f edit.sed  input.txt   #通过-f加脚本文件使用脚本文件中的命令

sed -n '3,4 p' input.txt     # m,n 这种中模式是定位用的，这里就是第3到第4行， 动作是p
sed '3,4 p' input.txt        # 看看不加-n的效果，这样可以用来重复一些行

sed  '3,4 d' input.txt  # d 表示delete

sed -n '/^A/=' input.txt  #返回以A开头的行。 ^表示开头， 而 = 表示行号

sed '/^A/=' input.txt  #思考一下不加-n时候的效果  

cat input.txt    # 如上的操作原文件未受到影响


sed 'y/ACGT/acgt/' input.txt   # y/.../.../ 字符转换，字符依次对应

sed 'y/ACGT/acg/' input.txt   # 报错

sed '/^A/y/ACGT/acgt/' input.txt    #只对以A开头的行进行转换， /^A/
sed '/^A/!y/ACGT/acgt/' input.txt    #不对以A开头的行进行转换，/^A/! ，注意叹号！

sed '2,4y/ACGT/acgt/' input.txt    #只对2-4行进行
sed '2,4!y/ACGT/acgt/' input.txt    #不对2-4行进行

sed -i 's/T/U/g' input.txt   # 加参数 -i 的时候在原文件上进行编辑

cat input.txt


sed -i '1iAddThisBeforeThe1stLine' input.txt   #在第一行前加入一行

cat input.txt

sed -i '$iAddThisBeforeTheLastLine' input.txt   #在第最后前加入一行

cat input.txt

```


### AWK 
AWK实际是一个编程语言，我们这里只讲一些简单的用法。 AWK可以和sed一样，对文件进行逐行处理，不同的是每一行可以被分为不同的域(field), 每行则是一个区（region）。而且，可以自定义区的分隔符，这样就不局限于逐行处理了，可以把多行当作一个region，每行当作一个field。这样AWK实际上是逐区进行操作， $0表示整个region，$1表示当前region的第一个field， $n表示第n个field. NR是当前区的序号。 
AWK的基本语法结构为: 
**awk '/pattern/ {action}' 输入文件**  

#### AWK初体验 

``` bash
echo -e '1\ta B\n25\tb c\n3\tfc df\n84\td ok\n15\txye rte\n6\tzf erhyhy\n7\tag hhh\n48\th ADF\n39\ti ERF\n10\tj B\n11\tK OKK\n12\tl BEST' > test.tsv

cat test.tsv    #查看一下，第一列和第二列之间制表符分割，第三列和第

awk '/a/ {print}' test.tsv      # 包含a的行被print出来
awk '/a/' test.tsv              # 默认的action是print $0
awk '{print $3}' test.tsv       # 默认的域分割符是制表符和空格，不区分此二者
awk '{print }' test.tsv         # print 是默认print $0
awk '/a/ {print $1}' test.tsv      # 包含a的行被print出来

awk -v FS="\t" '{print $1}' test.tsv   # 指定FS: Field Separator
awk -v FS="\t" '{print $2}' test.tsv
awk -v FS="\t" '{print $3}' test.tsv

awk -v FS="[ ]" '{print $1}' test.tsv  # 以空格作为FS的写法， 注意这里的空格以两个方括号括起来

awk -v RS="[ ]" '{print }' test.tsv    # 以空格作为RS：Region Separator的情况，可以看到，逐region print出来的

awk -v FS="\t" -v OFS="," '{print $1, $2}' test.tsv   # 指定OFS: Out Field Separator
awk 'BEGIN{FS="\t"; OFS="," } {print $1, $2}' test.tsv   # 另一种指定FS和OFS的方法, 使用BEGIN语句
awk 'BEGIN{FS="\t"; OFS="," } {print $1, $2} END{print "Done"}' test.tsv   # 使用END语句

awk -v FS="\t" -v OFS="," '{print $0}' test.tsv   # 理解一下，$0整个打印的时候

awk -v FS="\t" -v ORS="," '{print $1, $2}' test.tsv   # 指定ORS: Out Region Separator

awk '{if($1 > 20) print}'  test.tsv   # 使用命令

awk '$1 > 20'  test.tsv               # 使用模式匹配

awk '$1 ~ /e/' test.tsv      # 对第1列进行模式匹配
awk '$2 ~ /e/' test.tsv      # 对第2列进行模式匹配
awk '$3 ~ /e/' test.tsv      # 对第3列进行模式匹配 

awk '{if($3~/e/) print}' test.tsv 
awk '$1 > 10 {if($3~/e/) print}' test.tsv 

awk '$3~/e/ && $1 > 10' test.tsv       # 体会这两种方式都能实现这种效果
awk '{if($3~/e/ && $1 > 10 ) print}' test.tsv   # 

awk '{if(NR%2==0) $1=$1+100; print}' test.tsv   # % 表示求余， NR表示region的序号，NR除以2余0的行都加100
awk '{if(NR%2==0) {print $1+100}; print}' test.tsv   #看看这样的效果

awk '$3 ~ /e/ {x++} END{print x}' test.tsv   # 简单的编程，第三列包含e的行数
awk '{if($3 ~ /e/) x++} END{print x}' test.tsv   # 好吧，我觉得记住这种方法就OK了

awk '{x+=$1} END{print x}' test.tsv   #求第一列的和

#思考一下，怎么求，所有含有e的行的第一列的和？


awk 'BEGIN{print 10%3}'   #可以这样来试一些代码，后边不用跟文件
awk 'BEGIN{if("A">"a") {print "Yes" }else{ print "No"}}'
awk 'BEGIN{if(100>10) {print "Yes" }else{ print "No"}}'
awk 'BEGIN{print 100/3}'
awk 'BEGIN{print int(100/3)}'   #AWK里没有整除，可以这样实现整除
awk 'BEGIN{printf("%0.6f\n", 10.63)}'  # %s 表示字符串；%i表示整数；%e表示科学计数；%f为浮点数，%g选择浮点数和科学计数中占空间更少的
awk 'BEGIN{printf("%.2f\n", 10.639876)}'   #控制输出的样式
awk 'BEGIN{printf("%s: %.2f and %g \n", "The numbers are", 10.639876, 0.000000123)}'  
awk 'BEGIN{printf("%s: %.2f and %g \n", "The numbers are", 10.639876, 0.123)}'  


ls -l | awk '$5 > 100'             # awk也可以接受stdin；列出特定大小的文件
ls -l | awk '/.gz$/'               # 列出gz文件

```
#### AWK编程 

### vi 
##### 将代码粘贴到集群的文件 
在这个阶段，只需要知道怎么通过vi把在自己的电脑上编辑好的代码粘贴到集群上的文件就好了。 
  * 如下代码，回车之后就进入了编辑界面，这时候在命令模式
``` bash 
vi test.txt       # test.txt 改成你想要的文件名
```
  * 按键盘上的 i 进入输入模式，这样就能往文件里输入内容了
  * 将剪切板上的内容粘贴进去。比如putty是单击使用鼠标右键来实现粘贴
  * 如果需要做一些微小的修改，可以用方向键控制光标所在的位置，退格键可以逐个删除。需要修改的太多则可以将原来的文件删除，在自己电脑上编辑好了之后再整个粘贴进去。
  * 粘贴好了之后，进入命令模式。从编辑模式进入名模式按退出（ESC）键
  * 输入命令
``` bash
# 保存， 注意后边不能加 # 这样的注释
:w    
# 退出 
:q     
# 保存并退出
:wq    
# 强制退出，不保存
:q!   
 ```



##### 更多编辑技巧 
  * 命名模式下：按两次d删除整行
  * 命名模式下：:n 跳到第n行，:$ 跳到最后一行
  * 命令模式下：/pattern   向下查找
  * 命令模式下：？pattern   向上查找
  * 命令模式下：:set number  显示行号
  * 命令模式下：:set nonumber  不显示行号



### Shell 脚本 

### 正则表达式 

