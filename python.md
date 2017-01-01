# Python基础教程2

## 1. 基础

## 1.1 安装

### 1.2 交互式解析器

### 1.7 获得用户输入

### 1.8 函数

### 1.9 模块

#### 1.9.1 catch和复数

#### 1.9.2 回到__future

## 2. 列表和元组

## 3. 字符串

## 4. 字典

## 5. 条件循环

## 6. 抽象

## 7. 抽象2

## 8. 异常

## 9. 魔法方法、属性、迭代器

## 10. 充电

## 11. 文件和流

### 11.1 打开文件 

```
open(name[, mode[, buffering]])	// 传入文件名，返回一个文件对象
```

```
f = open(r'~/text/somefile.txt')	// linux
f = open(r'C:\text\somefile.txt')	// windows
```

mode：

* r: 读
* w: 写
* a: 追加
* b: 二进制（添加到其他模式）
* +: 读写模式（添加到其他模式）
* u: 换行符通用模式，该模式下所有换行符都被转为\n

> windows下换行为\r\n, linux为\n

buffering：

* 0或False：无缓冲，直接针对硬盘操作
* 1或True：有缓冲，操作内存，让程序更快，只有在flush和close时才更新硬盘数据
* \>1：缓冲区大小，以字节为单位
* <0：使用默认缓冲区大小

### 11.2 文件方法

标准输入法源sys.stdin, 输出源sys.stdout, 错误输出源sys.stderr, 都可以通过管道连接到其他输出输入源。

#### 11.2.1 读写

```python
f = open('somefile.txt', 'w')
f.write('Hello, ')
f.write('World!')
f.close()

f = open('somefile.txt', 'r')
f.read(4)	// 'Hell'
f.read()	// 'o, World!'
```

#### 11.2.2 管道

```shell
cat somefile.txt | python somescript.py
```

cat把somefile.txt输出到stdout，somescript.py的作用是从标准输入读，结果写到标准输出，管道符号将cat命令的stdout和somescripy.py的stdin连在一起。

```
import sys
text = sys.stdin.read()
words = text.split()
wordCount = len(words)
print 'Wordcount: ', worldCount
```

* seek(offset[, whence]): 定位到文件的偏移位置，offset表示偏移值，whence表示偏移起始点，=0为默认，表示从文件开始，=1相对于当前，=2相对于文件结尾。
* tell：返回当前文件读取位置。

#### 11.2.3 读写行

* readline(n): 读取单独的一行，n表示可以读取的这一行的最大值。
* readlines()
* writelines(): 传递一个字符串列表，程序不会增加新行，需要自己添加，为了跨平台，最好用os.linesep

#### 11.2.4 关闭文件

为了避免程序崩溃导致关闭不上，需要使用try...finally，更简单的方法是使用with

```python
with open('somefile.txt') as somefile:
	do_something(somefile)
```

文件在语句结束后自动关闭，即便是异常引起的。

> 上下文管理器(context manager)
>
> `__enter__`在进入with时被调用，返回值绑定到with后的变量
>
> `__exit__`离开方法时调用

### 11.3 文件迭代器 

#### 11.3.1 按字节处理

```python
f = open('file')
char f.read(1)
while char:		'''文件结尾时，char为False'''
	process(char)
	char = f.read(1)
f.close()
```

```python
f = open('file')
while True:
	char = f.read(1)
	if not char: break
	process(char)
f.close()
```

```python
f = open('file')
for char in f.read()	''' 遍历每个字符 '''
	process(char)
f.close()
```

#### 11.3.2 按行操作

```python
while True:
	line = f.readline()
	if not line: break
	process(line)
```

```python
f = open(file)
for line in f.readlines()
	process(line)
f.close()
```

#### 11.3.4 fileinput

readlines读大文件时占内存较大，可以用while和readline方法替换，更好的方法是使用fileinput类

```python
import fileinput
for line in fileinput.input(filename):
	process(line)
```

#### 11.3.5 文件迭代器

```python
f = open(filename)
for line in f:
	process(line)
f.close()
```

只有没有向文件写入，不关闭也是可以的，由python自身关闭，如下

```python
for line in open(filename)
	process(line)
```

sys.stdin也是可迭代的

```python
import sys
for line in sys.stdin:
	process(line)
```

可以对文件迭代器执行和普通迭代器相同的操作，如：

```python
lines = list(open(somefile))
first, second, third = open(somefile)
```



## 12. GUI

## 13. 数据库

## 14. 网络

## 15. web

## 16. 测试

## 17. 扩展Python

## 18. 程序打包

