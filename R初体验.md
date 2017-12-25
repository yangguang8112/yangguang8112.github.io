---
title: R初体验
date: 2016-08-20 16:47:53
tags: 算法/编程
---

[R](https://www.r-project.org/)是一套完备的专业数据分析和图形展示的统计环境和编程语言。R是免费的，并且支持所有常用的系统。基于R的[Bioconductor](http://www.bioconductor.org/)提供有大量的生物科学领域的数据分析软件包。

本教程目的在于让没有接触过R的人快速入门。 在阅读的过程中如果遇到不理解的地方，可以不用太纠结，继续往后读，可能读了后边的一些内容，就会理解前边的一些内容。 然而，对于提供的代码，最还还是要都输入到R控制台去看看效果。
<!--more-->
### R之初体验 
在R控制台逐行输入如下代码，不要想太多为什么，先看看是什么效果：
``` r
1

1:10

1 + 2

"2"

1 + "2"

1:10 + 1

c(1,3,13) + 1

c(1,3,13) * 5

pi

sin(pi)

log2(2)

log10(10)

log(2)

log(2,2)

log2(2,2)

round(pi,2)

(i+1)*(i+2)

(1i+3)*(2i+2)

sqrt(2)

2^(1/2)

(-1)^(1/2)

(0i - 1)^(1/2)

as.complex(-1)

x <- 1

x

y <- sin(x)

y

(z <- asin(y))    #加括号的时候会同时在屏幕输出对象的值

z



```


### R的一般语法结构 
 
``` r
object_name <- function(arguments)
```
R里的一切都可以看作是对象(object)，将一个或多个对象作为参数(arguments)，经过一系列运算输出特定的值的对象，就是函数(function)。  这里的 <- 是赋值符，就是把后边的结果取个名字叫object_name。 这里的object_name本身不是object，就像你的名字和你的关系一样。 这里的function可以是运算符的形式，也可以省略（也就是直接在控制台输入对象或者其名称时，就会返回相应的结果，比如对象的值或者摘要）。

另外，除了用赋值运算符以外，还可以用assign函数，如下：
``` r
assign("object_name", function(arguments))
```

需要区别的是这里的"object_name"是用引号引起来的，这和不引的时候是有区别的，这就牵涉到R的数据类型（data type）的问题。



### R数据类型 
R的数据类型（data type）有数值(Numeric)、字符(Character) 、复数（Complex）逻辑（Logical） 等。
使用mode查看
#### 数值(Numeric) 
``` r
x <- c(1, 3, 5)

x

is.numeric(x)

(y <- as.character(x))

is.numeric(y)

```
#### 字符(Character) 
``` r
x <- c("1", "2", "3")   # 创建一个字符型的向量

x

is.character(x)      #检查是否是字符型

as.numeric(x)        #转化成为数值型

as.numeric("A")      #强制把一些字符转化成为数值会产生NA值

is.numeric(as.numeric("A"))

as.character(as.numeric("A"))

is.numeric(as.character(as.numeric("A")))

```

#### 复数（Complex） 

``` r

sqrt(-1)   #数值型的-1是没法开根号的

mode(-1)

as.complex(-1)  #转成复数形式

sqrt(as.complex(-1))    #可以开根号了

sqrt(0i+1)   #加上 xi （x为数值），就是复数的形式

sqrt(3i-4) 

is.complex(3i-4)
```
#### 逻辑（Logical） 
``` r
TRUE  # TRUE 和 FALE就是逻辑值

5 < 10  

5 > 10

1:10 < 5   #返回逻辑值

sum(1:10 < 5)   #可以求和，TRUE就是1，FALSE就是0

as.character(1:10 < 5)

as.numeric(1:10 < 5)   #逻辑值转为数值

mode(c(TRUE, FALSE, NA))

mode(c(TRUE, FALSE, "NA"))  #== 标题 H5 
引号引起来的时候就是一个字符型

mode(c(TRUE, FALSE, 1))

mode(c(TRUE, FALSE, 1))

mode(c(1, 2, "1"))

mode(c(1, 2, "1"))

mode(c(1, 2, 0i))

0i

mode(c(1i, 2i, "1"))

```

== 一个向量只能是一种类型，在使用c()函数创建向量的时候，如果将不同类型的值放在一起，会有一个转化，这种优先级是这样的：字符型 > 复数 > 数值 > 逻辑 。一个向量只有一种类型，在列表读入的时候，也会遇到这种同一列里可能有几种类型的情况，这时候也会有转化，比如一列数字里如果具有含有标点的字符，这时候就会被当作字符型的读入。 而R在默认的情情况下会将字符转为因子型的，这样如果之间使用as.numeric转换成数值型的，就会发生错误，就需要先转成字符型的才行。 

这时候就可以使用如下的命令来关掉R的这功能，避免出现没有预料的情况。

``` r
options(stringsAsFactors=FALSE)
```

### R对象类型 
R常用的对象类型有向量(vector)、因子(factor)、数据框(data frame)、矩阵(array)、列表(list)、函数(function)等。
#### 向量(vector) 
按顺序排列的数值、字符、复数或者逻辑值的集合，就是向量。单独的一个这些类型的值出现的时候，可以看作是长度为一的向量。

``` r
c(1,4,7)

x <- c("cat", "dog",  "mouse", "human")

x[1]   #取出第一个元素，要注意的是R和Python等不一样的地方是，index是从1开始的而不是从0开始的。

x[-2]  #去掉第二个元素，这里-号的意义和Python里也是不一样的。

x[-c(1,3)]  

x[10]   #R里超过了向量的长度取值也不会报错，而是返回NA值


x[3] <- "mice"

x        # 可以看到，是很容易对向量里的值进行替换的


x[c(1,2,1,3,2,2,4,4)]  #再体会一下R向量取元素的方法
```
####  因子(factor) 
含有分组信息的向量，比较特殊。
``` r
x <- c("马", "羊",  "牛", "鸡", "鸭")

y <- factor(c("家畜", "家畜",  "家畜", "家禽", "家禽"))

mode(y)

as.numeric(y)

as.character(y)

(z <- factor(c("1", "2",  "4", "5", "7")))

as.numeric(z)

as.numeric(as.character(z))   # 这就是上文中讲到的问题，需要注意读入文本框的过程中可能把数字当字符


```

#### 数据框(data frame) 
二维的结构，每列是一个向量。可以看作是一种特殊的列表，即每个元素的长度一致。
``` r
options(stringsAsFactors=FALSE)
(x <- data.frame(A=1:10, B=letters[1:10]))  #创建一个数据框

x[1, ]     #数据框中的值的提取
x[ , 1]
x[1, 1]
x[1:3, 1:2]

x[1:3, 1:2]  <- data.frame(LETTERS[2:4], 3:5)   #可用同样的行列的数据框来替换数据框里同样行列的数据

x

x[ , "A"]  #也可以直接用列名

x[ ,3]       #体会这和向量里是不一样的，不能超出列表的长度来取值
x[1 ,3]
x[1 , ][3]

as.numeric(x[1 , ])[3]  #as.numeric 将只有一行的data frame转成了vector

as.numeric(x[1:2 , ])  #而对于两行的data frame却不能转了
```

####  矩阵(matrices) 
含有同一种数据类型的二维结构，其本质是vector，但是具有行和列的属性。
``` r
x  <- matrix(1:20, ncol=4, nrow=5)  #创建一个具有4列5行的矩阵

x[1， 3]  #矩阵取出元素的方法

x[4:2, c(2,4)]   #体会一下R里的灵活取元素的方法

x[4:2, c(2,4)]   <- "OK"  #和向量或者数据框一样可以替换

x  # 留意现在数据类型的变化

x[4:2, c(2,4)]   <- (1:6)^2  #同样长度的向量的替换，长度不等时重复使用

x   #变成字符型之后就变不回来了。

x[10]   # 这里就能看出矩阵其实是向量
```

####  数组(array) 
矩阵可以看作二维的数组，因此推广一下理解更高维数组
``` r
#略
```

####  列表(list) 
列表可以看作更为一般的向量，只是其元素可以是不同的数据类型，甚至是不同的对象类型，比如list的元素可以是list。
``` r
x=list(A=1:3, B=letters[1:10]) 

x
```

####  函数(function) 
将一系列代码放在一起，就成为函数，函数可以预设参数
``` r
f <- function(){
    print("A")
    print("Z")
    1+2
}

f

f()   #记住函数的调用方式


f <- function(x="A", y="B", a=1, b=2){   #可以先设置默认值
    print(x)
    print(y)
    a+b
}

f()

f("a", "b", 3, 5)  #参数可以按顺序写

f(b=10, x="OK")    #也可以不安顺序但是指明谁是谁

```
R的函数分为向量化的和非向量化的，向量化的函数对参数中的向量逐个元素地进行运算，逐一返回运算结果组成新的向量。运算符也是一种函数，只是用法和一般的函数有别。在使用函数时需要区分向量化的和非向量化的区别。 一般来说，常用的一些函数用习惯了就知道怎么用了，确实不需要大脑里经常去想哪个是向量化的哪个不是。

== 我们可以看到，我们上边区分了数据类型和对象类型，其实这两者很难去区分，数据也是对象。 


### 常规取子集 
前边已经说到了对象取子集，这里展开说一下。
##### R中对象取子集的基本语法 
``` r
my_object[row]            #一维对象取子集，如向量、因子、列表
my_object[row, col]       #二维对象取子集，如矩阵、数据框
my_object[row, col, dim]  #三维对象取子集，如三维数组
#以上语法可推广到多维数组
```

##### 三种可能方法 
取子集语法中的row、col、dim、等等可能有如下三种值： 
**1. 使用位置向量**，向量前边加"-"表示不包括   
**2. 使用等长的逻辑向量** 
**3. 使用元素名称向量**，当元素无名称时就不好用了 
``` r
X <- 1:10                   #创建一个向量
names(X) <- LETTERS[1:10]   #为每个元素命名, LETTERS为内置的26个字母按顺序组成的向量
X

X[1:4]                      # 使用位置取第 1-4 元素
X[1, 2, 4]                  # 
X[c(1, 2, 4)]               # 体会一下与上一条命令的区别

X[-c(1:4)]                  # 不包括 第1-4个元素。
X[-1:4]                     # 体会一下与上一条命令的区别
X[c(1,3,5,8,6,5,4,2,50)]
X[-c(1,3,5,8,6,5,4,2,50)]

X >5                        # 逻辑向量
X[X > 5]                    #使用逻辑向量取子集

X[c("B", "F", "J")]         #使用元素名称向量取子集


X <- data.frame(N=1:6, S=c("a", "b", "a", "c", "d", "b"))  #创建一个数据库
X 
X[c(1, 3, 6), ]             #使用位置向量
X[X$N < 5 & X$N > 1,  ]     #使用逻辑向量
X[X$S %in% c("a", "c"),  ]  #使用逻辑向量
X[ , c("S", "N")]           #通过元素名，这里是列名

row.names(X) <- LETTERS[1:6]   #更改行名
X[c("D", "E", "A"), ]         #使用行名



Y <- list(A=1:3, B=letters[1:5], C=sample(letters, 3))  #创建一个列表, sample用于随机抽取

Y[1]     #返回的是一个列表

Y[c("A","B")]

Y[1:2]     #


```


##### $符号的使用 
对于列表（包括数据框）可以使用$加元素的名称来取出相应的元素。
``` r
Y <- list(A=1:3, B=letters[1:5], C=sample(letters, 3))  #创建一个列表, sample用于随机抽取
Y$A 
Y$A[2]
Y$B[2:3]    

```

##### [[...]]的使用 
[[...]]可用于取出单一的元素，可递归
``` r
X <- 1:10                   #创建一个向量
names(X) <- LETTERS[1:10]   #为每个元素命名, LETTERS为内置的26个字母按顺序组成的向量
X

X[[1]]
X[[1:2]]  #递归失败


Y <- list(A=1:3, B=letters[1:5], C=sample(letters, 3))  #创建一个列表, sample用于随机抽取

Y[1]     #返回的是一个列表
Y[[1]]   #返回的是一个向量(元素)

Y[c("A","B")]    #取子集
Y[[c("A","B")]]  #递归失败，"A"元素里没有"B"元素

Y[1:2]     #
Y[[1:2]]   #递归成功，Y第一个元素里的第二个元素被取出

Z <- list(a = list(b = 9, c = "hello", d=list(e="3rd", f=list(g="4th"))), h = 1:5)
Z
Z[[c("a", "b")]]
Z[[c("a", "b", "c")]]
Z[[c("a", "d", "f")]]
Z[[c("a", "d", "f", "g")]]
Z$a$d$f$g
```



##### 给元素的对象赋值 
在取子集时赋值，即能改变对象中相应元素的值
``` r
X <- 1:10                   #创建一个向量
names(X) <- LETTERS[1:10]   #为每个元素命名, LETTERS为内置的26个字母按顺序组成的向量
X
X[5]  <- 1000
X
X["B"] <- "b"
X

Y <- list(A=1:3, B=letters[1:5], C=sample(letters, 3))  #创建一个列表, sample用于随机抽取
Y
Y[1:2] <- 1
Y
Y$B <- "abc"
Y

```

### R的运算 
#### 向量运算符 
**算术运算：**加(+)、减(-)、乘(*)、除以(/)、指数(^)、整除(%/%)、取模(%%)   
**比较运算：**相等()、不等(!=)、大于(>)、小于(<)、大于等于(>=)、小于等于(<=)   
**逻辑运算：**或(|)、且(&)、非(!)  
算术运算针对数值型或者复数型的对象进行加减乘数的基本算术，比较运算、逻辑运算结果为逻辑型。如： 
``` r
10+2*3+4/5-2*6

10 + 2*3 + 4/5 -2*6 + 2^3

10.3/2

11.3 %/% 2  

11.3 %% 2

1 == 2

1 <= 2

1 <= 2 & 1 == 2

1 <= 2 | 1 == 2

!(1 <= 2 & 1 == 2)

```




#### 向量运算 
如上提到的运算符都可以用于向量和向量之间，两个向量之前相同位置上的数值之间进行运算，运算结果按顺序返回生成新的向量。当两个向量长度不等时，较短的向量将重新从第一个元素开始重复使用，如此直到较长的向量所有元素都完成。这样当一个向量与长度为1的向量进行运算时，相当于每个元素都与这个值做同样的运算。
``` r
1:5 + 10:14

1:5 + 10:19

1:5 + 10:23  #当长度之间不是整数倍的时候会提示，但是依然有运算结果

```

#### 更多运算符 

&&   (且，非向量化的，用于长度为1的向量)；   
||   (或，非向量化的，用于长度为1的向量)；   
%in% (前一个向量的元素是否出现在后一个向量里,对前一个向量中每个元素依次判断返回向量结果)  
<-   (向左赋值) 
->   (向右赋值)  


``` r
TRUE && c(TRUE, FALSE)   # && 和 || 一般用于 if() 中
TRUE && c(FALSE, TRUE)
FALSE || c(TRUE, FALSE)
FALSE || c(FALSE, TRUE)

c(1,3,5) %in% c(2,3,5,6)
c(2,3,5,6) %in% c(1,3,5)

y <- 1 -> x
y
x
```



### 常用函数 
#### 获取帮助 
``` r
help(help)             # 查看帮助信息，能查看已载入的包的，需要注意的是对一些R的关键字需要用引号引起来，比如"if" "[[" 等
help.search("help")    # 对所有的包进行搜索，包括未载入的, 留意这里的参数必须是字符型的
? help                 # help()的快捷办法，
? "if"
?? help            
```
学会使用help函数是至关重要的，每次遇到不理解的地方，就查看一下help，积少成多，慢慢就成高手了。 
<html><p style="font-size:100%;color:black;" align="left"> <mark>往后遇到不理解的地方，都首先自己help一下。 我这里只提供一些函数的最基本的用法，更为广泛的用法都可以通过help来查看详细的参数说明。 大多数情况也也没有必要记住那些参数，因为很多参数一般情况下用不到。</mark> </p></html>


#### 工作目录 
``` r
getwd()                                # 查看当前的工作目录
  dir()                                # 查看当前工作目录下文件和文件夹
setwd('/BJPROJ/RNA/rna_test/XXXXXX')   # 设置工作目录为 /BJPROJ/RNA/rna_test/XXXXXX               
setwd("D:/XXXX")
setwd("D:XXXX")                      # 注意windows下的文件夹路径的两种写法。
```
#### 数学运算 
``` r
(x <- 1:10)
(y <- sample(1:100,10))
(z <- sample(1:100,10))     #先创建三个向量，用于展示
 
sum(x)      # 数值型向量的所有元素的总和
prod(x)     # 数值型向量的所有元素的乘积
mean(x)     # 数值型向量的所有元素的平均值
sd(x)       # 标准差 
median(x)   # 中位数
max(x)      # 最大值
min(x)      # 最小值
var(x)      # 方差    variance of x and the covariance or correlation of x and y if these are vectors
var(x, y)   # 协方差
cov(x, y)   # 协方差
cor(x, y)   # 皮尔逊相关性系数
cor(x, y, method = "spearman")   # spearman相关性系数

max(x, y, z)   
pmax(x, y, z)  #体会这种向量化的运算与max函数的区别
pmin(x, y, z)

which.max(z)   #返回向量中最大值的index
which.min(y)

sum(1:10)      #求和
cumsum(1:10)   #依次累加，看看效果

prod(1:10)     #求积
cumprod(1:10)  #累乘
```

#### 字符处理 
``` r
(x <- "ATTATGCATCGGC")
(y <- c("CGATTACGGC", "ATGAATGGC", "ATGTCGC", "ATNAGGGC"))
 
nchar(y)  # 字符串中的字符数，这也是向量化的函数 
length(y) # 向量y的长度，即元素个数

substr(y, 2,3)  #向量化操作，对每个元素都进行同样操作，返回对应顺序结果
substring(x,4)  #从第4个字符开始取至结尾

strsplit(x, "T")  # 以T为间隔讲字符串分割，返回结果为列表的形式
strsplit(y, "T")  # 以T为间隔讲字符串分割，返回结果为列表的形式 

grep("CG", y)    # 返回含特定字符的元素的序号(index)
grep("CG", y, value = TRUE)  #返回含特定字符的元素
grepl("CG", y)   # 向量化运算，返回逻辑型向量
grepl("^CG", y)  # ^表示起始
grepl("C.G", y)  # . 表示任意值
grepl("A.G", y)  # . 表示任意值
? regex          # 查看R正则表达式的用法
gsub("CG","_CG_", y)
 
(z <- letters[1:5])
 
paste(x, z, sep="_")   #向量化的函数，将两个向量的元素粘在一起，以特定字符相隔
paste(z, collapse="_") #设置collapse参数，将向量中所有的元素粘在一起，以特定字符相隔

```

#### sprintf 
这个函数用于将一些变量格式化并加入字符串中。
``` r
sprintf("%s is %f feet tall\n", "Sven", 7.1)
sprintf("%s is %f,: %d", "A", 99, 50)
  
sprintf("%f", pi) #以下展示不同形式的pi,不用都记住，知道有这么回事就好
sprintf("%.3f", pi)
sprintf("%1.0f", pi)
sprintf("%5.1f", pi)
sprintf("%05.1f", pi)
sprintf("%+f", pi)
sprintf("% f", pi)
sprintf("%-10f", pi) 
sprintf("%e", pi)
sprintf("%E", pi)
sprintf("%g", pi)
sprintf("%g",   1e6 * pi)
sprintf("%.9g", 1e6 * pi)
sprintf("%G", 1e-6 * pi)


(A <- sprintf("%03d", seq(0,100,5))) 
print(A)
```




#### 读写数据与基本作图 
这里我们使用rpkm.xls({{:入职:rpkm.xls|点击下载}}和id2symbol.tsv({{:入职:id2symbol.tsv|点击下载}}作为例子。下载后放到工作目录或者把工作目录设成存放此文件的文件夹。
``` r
rpkm <- read.delim("rpkm.xls", header=TRUE, sep='\t') # 这里的rpkm.xls 文件实际是纯文本的，并不是EXCEL格式   
head(rpkm)   #查看前几行
Lines <- readLines("rpkm.xls") # 逐行读入
head(Lines)  #注意与rpkm的区别

RPKM <- rpkm
names(RPKM) <- letters[1:10]  #更改表头，也就是列标题
head(RPKM)
rm(RPKM) #移除RPKM

write.table(rpkm, 'rpkm.tsv', row.names=FALSE, col.names=TRUE,  sep='\t', quote=F)  #保存数据框到表格，sep='\t' 这个参数在聚群上使用时需要注意，集群上默认的是空格，因此，需要使用制表符的时候，一定要加上。

plot(x=c(1,2,5) ,y= c(4,8,6))    #散点图，x和y为长度相等的向量，同一位置数值为一个点的横轴坐标。
plot(rpkm$T1,rpkm$T2)            #两个参数，不指明x ， y的时候，前边的为x，后边为y
plot(y=log(rpkm$T1+0.1), x=log(rpkm$T2+0.1), type='h',col='#FF0000')   #type指明图的类型

hist(log2(rpkm$T1+0.1))  #直方图

plot(density(log2(rpkm$T1+0.1))) #密度曲线图，先用density函数，再用plot函数
str(density(log2(rpkm$T1+0.1)))  #看看dengsity函数返回的对象的元素

pie(c(A=1,b=2,C=3),                  # 饼图
    labels = c("ok","byeye","p"),    # labels指定扇形的标记
    col=c("red","green","blue"))     # 指定颜色，颜色的选择可参考：http://colorbrewer2.org/

png('pie_test.png', type="cairo-png")   #图像的保存为png格式，这里默认输出到当前工作目录
pie(c(A=1,b=2,C=3), c("ok","byeye","p"), col=c("red","green","blue"))
dev.off()

pdf('/BJPROJ/RNA/rna_test/wanghong/pie_test.pdf')   #图像保存为pdf格式，文件名可以用绝对路径，改变输出到特定的目录
pie(c(A=1,b=2,C=3), c("ok","byeye","p"), col=c("red","green","blue"))
dev.off()

```

#### apply序列 
R里尽量避免for循环，运行速度比较慢。需要对一系列值做同样的操作的时候，可以使用apply序列的函数。
``` r
# rpkm <- read.delim("rpkm.xls", header=TRUE, sep='\t')    

rpkm$mean_Ts <- apply(rpkm[ , 2:4], 1, mean)   #apply 对数据框逐行运算，mean可以换成别的方程，可以自己定义
head(rpkm)  #看一下计算的结果
apply(rpkm[ , -1], 2, mean)   #对数据框的每列进行运算，第二个参数填2


lapply(1:10, function(x) x^2)  
lapply(as.list(1:10), function(x) x^2)   #lapply用于向量或列表，对列表每一个函数做同样的操作，依次返回结果，结果为列表

sapply(1:10, function(x) x^2)   
sapply(as.list(1:10), function(x) x^2)   #sapply用于向量或列表，对列表每一个函数做同样的操作，依次返回结果，结果为向量

rpkm$mean_W <- mapply(mean, rpkm$W1, rpkm$W2, rpkm$W3)  #mapply是sapply的多变量版本，返回的值是向量
head(rpkm)    #注意上边的这种用法是错误的
rpkm$mean_W <- mapply(function(x,y,z) mean(c(x,y,z)), x=rpkm$W1, y=rpkm$W2, z=rpkm$W3)
head(rpkm)    #注意mean函数的参数形式

```

#### sample 



#### 未分类 
``` r
2:9                         # : 用于产生间隔为1的等差数列, 从前一个数开始，最大不超过后一个数
2.1:9.2

seq(2, 13, 2)               # 产生等差数列，从第一个参数开始，以第三个参数为差，不大于第二个数
seq(2, 13, length=6)        # 使用length参数，产生指定长度的等差数列，差通过计算得到

rep(c(1,3,4), 3)            # 重复向量指定的次数
rep(c(1,3,4), each=3)       # 每个元素依次进行重复

unique(c(1,2,3,1,2,2,4,5))  # 返回唯一的值

combn(1:5, 2)                  # 求组合, 
combn(1:5, 3)

# rpkm <- read.delim("rpkm.xls", header=TRUE, sep='\t')    
ID2Symbol <- read.delim("id2symbol.tsv")
RPKM <- merge(ID2Symbol, rpkm, by="geneID", all.y=TRUE)  # merge 函数根据数据框的某一列将两个数据框联合在一起
head(RPKM)  # 用来查看前边的几行
tail(RPKM)  # 用来查看后边的几行
dim(RPKM)   # 查看行数和列数

names(ID2Symbol) <- c("ID", "Symbol")
RPKM <- merge(ID2Symbol, rpkm, by.x="ID", by.y="geneID")  # 
head(RPKM)
dim(RPKM)

str(rpkm)  #查看R对象的结构(structure), 可以看到每个元素的类型

x <- 1:10
stopifnot(x <= 10)     #stopifnot函数用于判断一个逻辑向量是否都是TRUE
stopifnot(x <= 5)
all(x <= 5)            #判断逻辑向量是否都是TRUE
any(x <=5)             #判断是否有一个为TRUE
which(x<=8 & x >=5)    #返回TRUE的值的index
```

### R中的流程控制 
常用的就是流程控制为for, if, else, while等，R里的流程控制语法中注意括号的用法。
``` r
for(i in 1:10){                                    # for对所有的元素依次执行
    if(i < 5){                                     # if做出判断
        print(sprintf("%s < 5", i))                
    } else if(i < 8) {                             # else if 部分可以没有，也可以有多个
        print(sprintf("%s >= 5 & %s < 8", i, i))
    } else {                                       # 
        print(sprintf("%s >= 8", i))
    }
}

for(i in 1:10){                                    
    if(i < 5){                                     # 
        next                                       # 跳出当前循环小节
        print(sprintf("%s < 5", i))                
    } else if(i < 8) {                             # 
        print(sprintf("%s >= 5 & %s < 8", i, i))
    } else {                                       # 
        print(sprintf("%s >= 8", i))
        break                                      # 终止整个for循环
    }
}


i=1
while(i <= 20){      # 满足条件的时候执行，需预先定义i，且循环中i应变化，否则即是死循环                            
    if(i < 5){                                     # 
        print(sprintf("%s < 5", i))                
        i = i + 2
    } else if(i < 8) {                             # 
        print(sprintf("%s >= 5 & %s < 8", i, i))
        i = i + 1
    } else {                                       # 
        print(sprintf("%s >= 8", i))
        i = i + 3
    }
}



```



### R包安装与运用 
R之所以强大，是因为有大量的R包，通过安装新的R包获得新的功能。R包根据不同的来源有不同的安装方法: 

``` r
##R包安装
#CRAN中的R包
install.packages(c("ggplot2", "plyr", "reshape", "gplots", "pheatmap"))  #可以看到这也是个向量化的函数， 可以同时安装多个包
install.packages("devtools")  #安装github中包需要用到devtools这个包

#BioConductor中的R包
source("https://bioconductor.org/biocLite.R")  #不行的话试着改成http://bioconductor.org/biocLite.R
biocLite("DESeq2")    #安装DESeq2， 其它包仿此

#github中的R包，一些CRAN中R包的最新开发中版本可以在github中找到
library(devtools)                    #载入devtools
install_github("hadley/ggplot2")     #安装github中的开发中版本

#其它来源的版本
#一般都会说明如何安装，也可以下载下来安装，这种情况比较少，具体问题具体再说。
install.packages("路径到文件或者网络连接到文件")


##R包的运用
library(gglot2)             # 载入R包，载入之后就能使用其中函数和数据了
browseVignettes("ggplot2")  # 查看手册

search()                    # 查看已经加载的R包以及对象
detach("package:pheatmap")  # 卸载已加载的R包，在需要换一个版本的包的时候可以用到

```
接下来会对一些包当中的常用函数做介绍，这些包的功能比这里介绍的要强大地多，通过考核之后可以自己去仔细研究。





### reshape包 
``` r
library(reshape)      #如果没有安装这个包，自己安装一下
# rpkm <- read.delim("rpkm.xls", header=TRUE, sep='\t') 
RPKM <- melt(rpkm, id=1)      #转换数据的格式，自己体会一下效果， id设置不变的列，这里是第一列不变
head(RPKM)

head( melt(rpkm, id=c(1,3,5)))   #1,3,5列不变，其它的melt

names(RPKM) <- c("geneID", "sample", "rpkm")       # 注意作为列名的rpkm和对象名rpkm的区别
Rpkm  <- cast(RPKM, geneID~sample, value="rpkm")   # 体会一下效果
head(Rpkm)
```

### plyr包 
``` r
library(plyr)
stat <- ddply(RPKM, .(sample), summarize, Mean=mean(rpkm), SD=sd(rpkm), SEM=sd(rpkm)/sqrt(length(rpkm)))
#使用ddply来根据一列或多列来求另一列按这两列的值来分类的各种值，可自定义函数。
# 这里是根据样本一列，求一个样本中所有基因的表达均值等等的
head(stat)   

# 大家可以自己想一下，怎么分基因求几个样本中的均值和标准差等等的

Stat <- ddply(RPKM, .(sample), Mean=mean(rpkm), SD=sd(rpkm), SEM=sd(rpkm)/sqrt(length(rpkm)))
head(Stat)   # 没有summarize是不可以的
```

### pheatmap包 
``` r
library(pheatmap)
test_data <- rpkm[apply(rpkm[ , 2:10], 1, sd) !=0, ] #有点复杂，自己慢慢领会，领会不了也没关系，这里重点在pheatmap
pheatmap(test_data[1:100, 2:10], scale="row", cluster_cols = FALSE)  #做热图

pdf("heatmap.pdf", height=7, width=7)
pheatmap(test_data[1:100, 2:10], scale="row", cluster_cols = FALSE)  #做热图
dev.off()

pdf("heatmap2.pdf", height=10, width=7, onefile=FALSE)   #　行数比较多，增加高度
pheatmap(test_data[1:100, 2:10], scale="row", cluster_cols = FALSE, labels_row = test_data$geneID, fontsize_row = 5)  #labels_row可以手动设置行的标记  ， fontsize_row  设置字号，这里变小
dev.off()




```










### ggplot2画图 
ggplot2是很强大的作图R包，学了ggplot2之后似乎都可以把基本的画图函数忘掉了。可以到[ggplot2文档页](http://docs.ggplot2.org/current/)学习各种图的绘制，那里有很多的例子。 可以优先看一下[geom_point](http://docs.ggplot2.org/current/geom_point.html)、[geom_histogram](http://docs.ggplot2.org/current/geom_histogram.html)、[geom_density](http://docs.ggplot2.org/current/geom_density.html)、[geom_boxplot](http://docs.ggplot2.org/current/geom_boxplot.html)、[geom_bar](http://docs.ggplot2.org/current/geom_bar.html),以下是一些例子，可以试试结合各种元素画出不同风格和外观的图。
``` r
library(ggplot2)  
library(reshape) 
library(RColorBrewer)

rpkm_mel <- melt(rpkm, id=1)                                                 # ? melt
rpkm_mel$group <- substr(rpkm_mel$variable, 1, 1)                  #选取样本名的首字母作为组名（示例用，随便选的）
rpkm_mel$group <- factor(rpkm_mel$group, levels=c("W","T"))  #改变group的顺序

g <-         #将后边的ggplot返回的对象“命名”为g
ggplot() +   
    geom_boxplot(data=rpkm_mel, aes(x=variable, y=log(value)))
g   #使用g来喊出图来

g <-         
ggplot() +   
    geom_boxplot(data=rpkm_mel, aes(x=variable, y=log(value), colour=variable))   # 增加colour
g   

g <-         #将后边的ggplot返回的对象“命名”为g
ggplot() +   
    geom_boxplot(data=rpkm_mel, aes(x=variable, y=log(value), colour=variable, fill=group))  #增加fill
g   #使用g来喊出图来

g <-         #将后边的ggplot返回的对象“命名”为g
ggplot() +   
    geom_boxplot(data=rpkm_mel, aes(x=variable, y=log(value), colour=variable, fill=group)) + #增加fill
    scale_fill_manual(values=c("red","green"))   # 手动设置填充颜色
g   #使用g来喊出图来


g <-         #将后边的ggplot返回的对象“命名”为g
ggplot() +   
    geom_boxplot(data=rpkm_mel, aes(x=variable, y=log(value), colour=variable, fill=group)) + #增加fill
    scale_fill_manual(values=c("red","green")) +  # 手动设置填充颜色
    scale_x_discrete(labels=c(1:9))    #设置横坐标

g   #使用g来喊出图来




g <-         #将后边的ggplot返回的对象“命名”为g
ggplot() +   
    geom_boxplot(data=rpkm_mel, aes(x=variable, y=log(value), colour=variable, fill=group))+
    xlab("samples")+
    ylab("log RPKM") +
    ggtitle("boxplot of gene expression") +
    scale_x_discrete(labels=c(1:9))  +
    scale_y_continuous(limits=c(-10,10), breaks=seq(-10,10,by=2.5))+
    scale_fill_manual(values=c("red","green")) +
    theme_bw() +
    theme(legend.position='top',              # ? theme查看更多的theme参数
          legend.direction="horizontal",
          axis.text.x=element_text(size=20)
      )

ggsave("boxplot.pdf", plot=g)


g <-         #将后边的ggplot返回的对象“命名”为g
ggplot() +   
    geom_histogram(data=rpkm_mel, aes(x=log(value), fill=variable), position='dodge',binwidth=1)  + #控制bin的宽度
    facet_grid(.~group) +      
    xlab("log RPKM")+
    ggtitle("histogram of gene expression") +
    scale_fill_manual(values=brewer.pal(9, 'Set1'), name='sample') +   #用 ? RColorBrewer  查看这个颜色的信息
    theme_bw() +
        theme(legend.position='top',
        legend.direction="horizontal",
        axis.text.x=element_text(size=20)
 )

 ggsave("histogram.pdf", plot=g)

g <-         #将后边的ggplot返回的对象“命名”为g
ggplot() +   
    geom_point(data=rpkm_mel, aes(x=variable, y=log(value), colour=variable, fill=group), position='jitter')+   v#jitter 会在坐标上随机加上一些偏离
    xlab("sample")+
     ylab("log RPKM") +
     ggtitle("scatterplot of gene expression") +
     scale_x_discrete(labels=c(1:9))  +
     scale_y_continuous(limits=c(-10,10), breaks=seq(-10,10,by=2.5))+
     scale_fill_manual(values=c("red","green")) +
     theme_bw() +
     theme(legend.position='top',
         legend.direction="horizontal",
         axis.text.x=element_text(size=20)
 )
 ggsave("scatterplot.pdf", plot=g, useDingbats=FALSE)
```   

散点图中包括圆圈(shape=1,10,13,16,19,20等)时，在一些可能阅读器中可能会显示为q，这时设置：useDingbats=FALSE 

到这个网站上学习更多： http://docs.ggplot2.org/current/


### R脚本 
除了如上在R控制台运行R的代码以外，还能把代码放到一个文件里，作为脚本的形式运行,比如这个test.R脚本。
``` r
print("Hi RNA")

cat("Hi Hong")
```

``` bash
Rscript test1.R
```

另外，还能将一些变量作为参数的形式传入，只需要用commandArgs函数即可。(trailingOnly = FALSE)

``` r
args <- commandArgs(TRUE)

person1 <- args[1]
person2 <- args[2]

#Say Hellow to Person 1
cat(sprintf("Hi %s \n", person1))

#Say Hellow to Person 2
cat(sprintf("Hi %s \n", person2))

```

``` bash
Rscript test2.R  Hong RNA
```




