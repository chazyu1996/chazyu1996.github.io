---
title: "Shell技巧"
date: 2019-07-08T10:02:56+08:00
tags:
  - linux
categories:
  - 技术文章
thumbnail: /images/thumbnail/code.jpg
toc: true
---
<!--more-->

# 入参和默认变量

对于shell脚本而言，有些内容是专门用于处理参数的，他们都有特定的含义 ，例如：

```
/root/test.sh arg1 arg2
$0  # 脚本名
$1  # 第一个参数
$2  # 第二个参数
$#  # 脚本后面跟的参数个数
$@  # 所有参数，并且可以被遍历
$*  # 所有参数，且作为整体，和$*很像，但是有区别
$$  # 当前脚本的进程ID
$?  # 上一条命令的退出状态
```

# 变量

给变量赋值，使用等号即可，但是*等号两边千万不要有空格*，等号右边有空格的字符串也必须用引号引起来：

```
arg1="hello world"  # 直接赋值
unset arg1   # 取消赋值
echo "para1 is $arg1"  # 调用变量
echo "para1 is ${arg1}"  #调用变量
```

# 执行命令

```
a=`ls`  # 直接执行
# 或
a=$(a)

# 计算
echo "1+1=$((1+1))"

# 引用单条执行结果
a="ls"
echo "$($a)"

# 引用多个执行结果
a="ls;pwd"
echo "$(eval $a)"  # 使用了eval，将a的内容都作为命令来执行。
```

# 条件分支

```
if [ $? -eq 0 ];
then
    eho "success"
elif [ $? -eq 1 ];
then
    echo "failed,code is 1"
else
    echo "other code"
fi


name="aa"
case $name in
    "aa")
    echo "name is aa"
    ;;
    "")
    echo "name is null"
    ;;
    "bb")
    echo "name is bb"
    ;;
    *)
    echo "name is other"
    ;;
esac

# 多条件
# -o或者||表示或。这里也有一些常见的条件判定。
if [ 10 -gt 5 -o 10 -gt 4 ];then
    echo "10>5 or 10>4"
fi

if [ 10 -gt 5 ] || [ 10 -gt 4 ];then
    echo "10>5 or 10>4"
fi
```

### 条件

> -o or 或者，同||
> -a and 与，同&&
> ! 非

### 整数判断

> -eq 两数是否相等
> -ne 两数是否不等
> -gt 前者是否大于后者（greater then）
> -lt 前面是否小于后者（less than）
> -ge 前者是否大于等于后者（greater then or equal）
> -le 前者是否小于等于后者（less than or equal）

### 字符串判断str1 exp str2

> -z "$str1" str1是否为空字符串
> -n "$str1" str1是否不是空字符串
> "$str1" == "$str2" str1是否与str2相等
> "$str1" != "$str2" str1是否与str2不等
> "$str1" =~ "str2" str1是否包含str2

### 文件目录判断：filename

> -f $filename 是否为文件
> -e $filename 是否存在
> -d $filename 是否为目录
> -s $filename 文件存在且不为空
> ! -s $filename 文件是否为空

# 循环

```
#遍历输出脚本的参数
for i in $@;do
    echo $i
done


for ((i = 0 ; i < 10 ; i++ ));do
    echo $i
done


for i in {1..5};do
    echo "Welcome $i"
done


while["$ans"!="yes"]
do
    read -p "please input yes to exit loop:" ans
done


ans=yes
until[ $ans !="yes"]
do
    read -p "please input yes to exit loop:" ans
done


for i in{5..15..3};do
    echo "number is $i"
done

```

# 函数

```
myfunc(){
    echo "hello world $1"
}


function myfunc(){
    echo "hello world $1"
}
```

# 返回值

通常函数的return返回值只支持0-255

```
function myfunc(){
    local myresult='some value'
    echo $myresult
}
val=$(myfunc) #val的值为some value
```

通过return的方式适用于判断函数的执行是否成功

```
function myfunc(){
    #do something
    return0
}
if myfunc;then
    echo "success"
else
    echo "failed"
fi
```

# 注释

```
#!/bin/bash
# 这是一行注释
:'
这是
多行
注释
'
ls
:<<EOF
这也可以
达到
多行注释
的目的
EOF
```

# 日志保存

### 方式一，将标准输出保存到文件中，打印标准错误：

> `./test.sh > log.dat`
> 这种情况下，如果命令执行出错，错误将会打印到控制台。所以如果你在程序中调用，这样将不会讲错误信息保存在日志中。

### 方式二，标准输出和标准错误都保存到日志文件中：

> `./test.sh > log.dat 2>&1`

### 方式三，保存日志文件的同时，也输出到控制台：

> `./test.sh |tee log.dat`

# 脚本执行

> `./test.sh` 直接执行
> `sh test.sh`  在子进程中执行
> `sh -x test.sh`  debug模式，会在终端打印执行到命令，适合调试
> `source test.sh` test.sh在父进程中执行
> `. test.sh` 不需要赋予执行权限，临时执行

# 脚本退出码

```
#!/bin/bash
function myfun(){
if[ $# -lt 2 ]
then
    echo "para num error"
    exit 1
fi
echo "ok"
exit 2
}

if[ $# -lt 1 ]
then
    echo "para num error"
    exit 1
fi

returnVal=`myfun aa`
echo "end shell"
exit 0
```

这里需要特别注意的一点是，使用 returnVal=\`myfun aa\`

这样的句子执行函数，即便函数里面有exit，它也不会退出脚本执行，而只是会退出该函数，这是因为exit是退出当前进程，而这种方式执行函数，相当于fork了一个子进程，因此不会退出当前脚本。最终结果就会看到，无论你的函数参数是什么最后end shell都会打印。

