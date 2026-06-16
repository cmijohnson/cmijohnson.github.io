---
title: Python 常见考点复习文档
date: 2026-06-16 09:33:00
author: admin
tags: [Python]
categories: 基础语言
status: published
---
# Python 常见考点复习文档

> 适合期末考试 / 机考 / 选择填空 / 程序阅读 / 编程题快速复习。  
> 复习思路：**先会读代码，再会改代码，最后会写常见小题。**

---

## 0. 考前总览：Python 最常考什么？

| 模块     | 常见考法                                      | 重点程度 |
| -------- | --------------------------------------------- | -------- |
| 基础语法 | 变量、输入输出、缩进、注释                    | ★★★★★    |
| 数据类型 | int、float、str、bool、list、tuple、dict、set | ★★★★★    |
| 运算符   | 算术、比较、逻辑、成员、身份运算              | ★★★★★    |
| 分支循环 | if、for、while、break、continue               | ★★★★★    |
| 字符串   | 索引、切片、常用方法、格式化输出              | ★★★★★    |
| 列表     | 增删改查、切片、排序、遍历                    | ★★★★★    |
| 字典     | 键值对、遍历、统计频次                        | ★★★★☆    |
| 函数     | 定义、参数、返回值、作用域                    | ★★★★★    |
| 文件操作 | open、read、write、with                       | ★★★★☆    |
| 异常处理 | try-except-finally                            | ★★★☆☆    |
| 面向对象 | 类、对象、构造方法、self                      | ★★★☆☆    |
| 常见算法 | 求和、最大最小、素数、水仙花、排序、查找      | ★★★★★    |

---

## 1. Python 程序基本结构

### 1.1 注释

```python
# 单行注释

'''
多行注释
也可以这样写
'''
```

考试容易问：

```python
# 这行不会执行
print("hello")  # 这部分也不会执行
```

---

### 1.2 缩进

Python 不用 `{}` 表示代码块，靠**缩进**。

```python
if True:
    print("进入 if")
    print("这一行也属于 if")
print("这一行不属于 if")
```

易错点：

```python
if True:
print("hello")   # 错误：没有缩进
```

---

## 2. 输入与输出

## 2.1 print 输出

```python
print("hello")
print(1, 2, 3)
```

输出：

```text
hello
1 2 3
```

### print 的 sep 和 end

```python
print(1, 2, 3, sep="-")
print("hello", end=" ")
print("world")
```

输出：

```text
1-2-3
hello world
```

解释：

| 参数  | 含义                                  |
| ----- | ------------------------------------- |
| `sep` | 多个输出内容之间用什么隔开，默认空格  |
| `end` | 一次 print 结束后用什么结尾，默认换行 |

---

## 2.2 input 输入

```python
name = input("请输入姓名：")
print(name)
```

重点：`input()` 输入的内容**永远是字符串 str**。

```python
x = input("请输入数字：")
print(type(x))
```

即使输入 `123`，类型也是：

```python
<class 'str'>
```

如果要做数学运算，要转类型：

```python
a = int(input("请输入整数："))
b = float(input("请输入小数："))
```

---

## 3. 变量与数据类型

## 3.1 常见数据类型

| 类型  | 例子              | 说明             |
| ----- | ----------------- | ---------------- |
| int   | `10`              | 整数             |
| float | `3.14`            | 小数             |
| str   | `'abc'`、`"abc"`  | 字符串           |
| bool  | `True`、`False`   | 布尔值           |
| list  | `[1, 2, 3]`       | 列表，可修改     |
| tuple | `(1, 2, 3)`       | 元组，不可修改   |
| dict  | `{"name": "Tom"}` | 字典，键值对     |
| set   | `{1, 2, 3}`       | 集合，去重、无序 |

---

## 3.2 type 查看类型

```python
x = 10
print(type(x))
```

输出：

```python
<class 'int'>
```

---

## 3.3 类型转换

```python
int("123")       # 123
float("3.14")    # 3.14
str(100)         # "100"
list("abc")      # ['a', 'b', 'c']
```

易错：

```python
int("3.14")      # 错，因为 "3.14" 不是整数形式
int(3.14)        # 3，小数部分直接丢掉，不是四舍五入
```

---

## 4. 运算符

## 4.1 算术运算符

| 运算符 | 含义   | 例子     | 结果  |
| ------ | ------ | -------- | ----- |
| `+`    | 加     | `3 + 2`  | `5`   |
| `-`    | 减     | `3 - 2`  | `1`   |
| `*`    | 乘     | `3 * 2`  | `6`   |
| `/`    | 真除法 | `5 / 2`  | `2.5` |
| `//`   | 整除   | `5 // 2` | `2`   |
| `%`    | 取余   | `5 % 2`  | `1`   |
| `**`   | 幂运算 | `2 ** 3` | `8`   |

重点区别：

```python
print(5 / 2)   # 2.5
print(5 // 2)  # 2
print(5 % 2)   # 1
```

---

## 4.2 比较运算符

| 运算符 | 含义         |
| ------ | ------------ |
| `>`    | 大于         |
| `<`    | 小于         |
| `>=`   | 大于等于     |
| `<=`   | 小于等于     |
| `==`   | 判断是否相等 |
| `!=`   | 判断是否不等 |

易错点：

```python
x = 5      # 赋值
x == 5     # 判断 x 是否等于 5
```

---

## 4.3 逻辑运算符

| 运算符 | 含义 | 例子           |
| ------ | ---- | -------------- |
| `and`  | 并且 | 两边都真才真   |
| `or`   | 或者 | 有一个真就真   |
| `not`  | 取反 | 真变假，假变真 |

```python
age = 20
print(age >= 18 and age <= 60)  # True
```

Python 支持连续比较：

```python
if 18 <= age <= 60:
    print("合法年龄")
```

---

## 4.4 成员运算符

```python
print('a' in 'abc')       # True
print(4 in [1, 2, 3])     # False
print('x' not in 'abc')   # True
```

---

## 4.5 身份运算符

```python
a is b
```

判断两个变量是否指向同一个对象。

一般考试更常用的是：

```python
a == b
```

区别：

| 写法 | 判断内容         |
| ---- | ---------------- |
| `==` | 值是否相等       |
| `is` | 是否是同一个对象 |

---

## 5. 分支结构 if

## 5.1 基本格式

```python
score = 85

if score >= 90:
    print("优秀")
elif score >= 60:
    print("及格")
else:
    print("不及格")
```

执行逻辑：

1. 先判断 `if`
2. 不满足再判断 `elif`
3. 都不满足才执行 `else`

---

## 5.2 常见题型：判断奇偶数

```python
n = int(input("请输入一个整数："))

if n % 2 == 0:
    print("偶数")
else:
    print("奇数")
```

---

## 5.3 常见题型：成绩等级

```python
score = int(input("请输入成绩："))

if score >= 90:
    print("A")
elif score >= 80:
    print("B")
elif score >= 70:
    print("C")
elif score >= 60:
    print("D")
else:
    print("E")
```

注意：顺序不能乱。  
如果先写 `score >= 60`，那么 95 分也会被归到及格。

---

## 6. 循环结构

## 6.1 for 循环

适合：知道循环次数，或者遍历序列。

```python
for i in range(5):
    print(i)
```

输出：

```text
0
1
2
3
4
```

---

## 6.2 range 函数

```python
range(stop)
range(start, stop)
range(start, stop, step)
```

例子：

```python
list(range(5))          # [0, 1, 2, 3, 4]
list(range(1, 5))       # [1, 2, 3, 4]
list(range(1, 10, 2))   # [1, 3, 5, 7, 9]
list(range(10, 1, -2))  # [10, 8, 6, 4, 2]
```

重点：`range` 左闭右开，包含左边，不包含右边。

---

## 6.3 while 循环

适合：不知道循环次数，只知道循环条件。

```python
i = 1
while i <= 5:
    print(i)
    i += 1
```

---

## 6.4 break 和 continue

### break：直接结束整个循环

```python
for i in range(1, 6):
    if i == 3:
        break
    print(i)
```

输出：

```text
1
2
```

### continue：跳过本轮，继续下一轮

```python
for i in range(1, 6):
    if i == 3:
        continue
    print(i)
```

输出：

```text
1
2
4
5
```

---

## 6.5 常见题型：求 1 到 n 的和

```python
n = int(input("请输入 n："))
s = 0

for i in range(1, n + 1):
    s += i

print(s)
```

---

## 6.6 常见题型：求 1 到 n 的偶数和

```python
n = int(input("请输入 n："))
s = 0

for i in range(1, n + 1):
    if i % 2 == 0:
        s += i

print(s)
```

---

## 7. 字符串 str

## 7.1 字符串定义

```python
s1 = 'hello'
s2 = "hello"
s3 = '''hello'''
```

---

## 7.2 索引

```python
s = "python"
print(s[0])   # p
print(s[1])   # y
print(s[-1])  # n
print(s[-2])  # o
```

理解：

```text
 正向索引： 0   1   2   3   4   5
 字符：     p   y   t   h   o   n
 反向索引：-6  -5  -4  -3  -2  -1
```

---

## 7.3 切片

格式：

```python
字符串[开始:结束:步长]
```

重点：切片也是左闭右开。

```python
s = "python"

print(s[0:2])    # py
print(s[1:4])    # yth
print(s[:3])     # pyt
print(s[3:])     # hon
print(s[::-1])   # nohtyp，字符串反转
```

---

## 7.4 字符串常用方法

| 方法             | 作用                    | 例子                      |
| ---------------- | ----------------------- | ------------------------- |
| `len(s)`         | 求长度                  | `len("abc")` 得 3         |
| `s.upper()`      | 转大写                  | `"abc".upper()`           |
| `s.lower()`      | 转小写                  | `"ABC".lower()`           |
| `s.strip()`      | 去掉两边空白            | `" hi ".strip()`          |
| `s.replace(a,b)` | 替换                    | `"abc".replace("a", "x")` |
| `s.split()`      | 分割                    | `"a b c".split()`         |
| `"-".join(list)` | 拼接                    | `"-".join(["a", "b"])`    |
| `s.find(x)`      | 查找位置，找不到返回 -1 | `"abc".find("b")`         |
| `s.count(x)`     | 统计次数                | `"banana".count("a")`     |

---

## 7.5 字符串不可变

```python
s = "abc"
s[0] = "x"   # 错误
```

如果要改，只能生成新字符串：

```python
s = "abc"
s = "x" + s[1:]
print(s)  # xbc
```

---

## 8. 列表 list

## 8.1 定义列表

```python
nums = [1, 2, 3, 4]
names = ["Tom", "Jerry"]
mix = [1, "a", 3.14]
```

列表特点：

1. 有顺序
2. 可重复
3. 可修改

---

## 8.2 列表索引和切片

```python
lst = [10, 20, 30, 40]

print(lst[0])     # 10
print(lst[-1])    # 40
print(lst[1:3])   # [20, 30]
```

---

## 8.3 增删改查

### 增加元素

```python
lst = [1, 2, 3]
lst.append(4)       # 末尾加 4
lst.insert(1, 99)   # 在下标 1 的位置插入 99
print(lst)
```

### 删除元素

```python
lst = [1, 2, 3, 2]

lst.remove(2)   # 删除第一个 2
x = lst.pop()   # 删除并返回最后一个元素
lst.clear()     # 清空列表
```

### 修改元素

```python
lst = [1, 2, 3]
lst[0] = 100
print(lst)  # [100, 2, 3]
```

### 查找元素

```python
lst = [10, 20, 30]
print(20 in lst)       # True
print(lst.index(20))   # 1
```

---

## 8.4 列表排序

```python
lst = [3, 1, 4, 2]
lst.sort()
print(lst)   # [1, 2, 3, 4]
```

降序：

```python
lst.sort(reverse=True)
print(lst)   # [4, 3, 2, 1]
```

`sorted()` 不改变原列表，返回新列表：

```python
lst = [3, 1, 4, 2]
new_lst = sorted(lst)
print(lst)      # [3, 1, 4, 2]
print(new_lst)  # [1, 2, 3, 4]
```

区别：

| 写法          | 是否改变原列表 | 返回值 |
| ------------- | -------------- | ------ |
| `lst.sort()`  | 改变           | `None` |
| `sorted(lst)` | 不改变         | 新列表 |

---

## 8.5 遍历列表

```python
lst = [10, 20, 30]

for x in lst:
    print(x)
```

如果需要下标：

```python
lst = [10, 20, 30]

for i in range(len(lst)):
    print(i, lst[i])
```

更推荐：

```python
for i, x in enumerate(lst):
    print(i, x)
```

---

## 8.6 列表推导式

普通写法：

```python
lst = []
for i in range(1, 6):
    lst.append(i * i)
```

推导式写法：

```python
lst = [i * i for i in range(1, 6)]
print(lst)  # [1, 4, 9, 16, 25]
```

带条件：

```python
lst = [i for i in range(1, 11) if i % 2 == 0]
print(lst)  # [2, 4, 6, 8, 10]
```

---

## 9. 元组 tuple

元组和列表很像，但元组**不可修改**。

```python
t = (1, 2, 3)
print(t[0])
```

错误写法：

```python
t[0] = 100   # 错误，元组不可变
```

只有一个元素的元组：

```python
t = (1,)
```

注意：

```python
t = (1)
print(type(t))  # int，不是 tuple
```

---

## 10. 字典 dict

## 10.1 字典定义

```python
student = {
    "name": "Tom",
    "age": 18,
    "score": 90
}
```

字典特点：

1. 用键值对保存数据
2. 键不能重复
3. 通过键取值

---

## 10.2 字典取值

```python
student = {"name": "Tom", "age": 18}

print(student["name"])
print(student.get("name"))
```

区别：

```python
student["sex"]       # 键不存在会报错
student.get("sex")   # 键不存在返回 None
```

也可以设置默认值：

```python
student.get("sex", "未知")
```

---

## 10.3 字典增删改

```python
student = {"name": "Tom", "age": 18}

student["score"] = 90     # 新增
student["age"] = 20       # 修改
del student["name"]       # 删除
```

---

## 10.4 遍历字典

```python
student = {"name": "Tom", "age": 18}

for key in student:
    print(key, student[key])
```

更常见：

```python
for key, value in student.items():
    print(key, value)
```

只遍历键：

```python
for key in student.keys():
    print(key)
```

只遍历值：

```python
for value in student.values():
    print(value)
```

---

## 10.5 高频题：统计字符出现次数

```python
s = "banana"
d = {}

for ch in s:
    d[ch] = d.get(ch, 0) + 1

print(d)
```

输出：

```python
{'b': 1, 'a': 3, 'n': 2}
```

核心套路：

```python
d[x] = d.get(x, 0) + 1
```

意思是：

1. 如果 x 以前出现过，就取原来的次数
2. 如果没出现过，就按 0 处理
3. 然后次数加 1

---

## 11. 集合 set

集合特点：

1. 无序
2. 不重复
3. 常用于去重

```python
s = {1, 2, 3, 3, 3}
print(s)  # {1, 2, 3}
```

列表去重：

```python
lst = [1, 2, 2, 3, 3, 3]
lst = list(set(lst))
print(lst)
```

注意：集合没有下标。

```python
s = {1, 2, 3}
# s[0]  错误
```

---

## 12. 函数

## 12.1 函数定义

```python
def 函数名(参数):
    函数体
    return 返回值
```

例子：

```python
def add(a, b):
    return a + b

result = add(3, 5)
print(result)
```

---

## 12.2 return 的作用

`return` 表示：

1. 返回结果
2. 结束函数

```python
def test():
    return 1
    print("hello")  # 不会执行
```

---

## 12.3 没有 return 的函数

```python
def hello():
    print("hello")

x = hello()
print(x)
```

输出：

```text
hello
None
```

如果函数没有 `return`，默认返回 `None`。

---

## 12.4 参数类型

### 位置参数

```python
def add(a, b):
    return a + b

add(1, 2)
```

### 默认参数

```python
def greet(name="同学"):
    print("你好", name)

greet()
greet("Tom")
```

### 关键字参数

```python
def info(name, age):
    print(name, age)

info(age=18, name="Tom")
```

---

## 12.5 局部变量和全局变量

```python
x = 10

def f():
    x = 20
    print(x)

f()        # 20
print(x)   # 10
```

函数内部的 `x` 是局部变量，不影响外面的 `x`。

如果一定要在函数里改全局变量：

```python
x = 10

def f():
    global x
    x = 20

f()
print(x)  # 20
```

---

## 12.6 递归函数

递归：函数自己调用自己。

必须有：

1. 递归出口
2. 递归调用

### 求阶乘

```python
def fact(n):
    if n == 1:
        return 1
    return n * fact(n - 1)

print(fact(5))  # 120
```

理解：

```text
fact(5) = 5 * fact(4)
fact(4) = 4 * fact(3)
fact(3) = 3 * fact(2)
fact(2) = 2 * fact(1)
fact(1) = 1
```

---

## 13. 文件操作

## 13.1 open 基本格式

```python
f = open("test.txt", "r", encoding="utf-8")
content = f.read()
f.close()
```

更推荐：

```python
with open("test.txt", "r", encoding="utf-8") as f:
    content = f.read()
```

`with` 会自动关闭文件。

---

## 13.2 文件打开模式

| 模式 | 含义                                   |
| ---- | -------------------------------------- |
| `r`  | 只读，文件不存在会报错                 |
| `w`  | 写入，会清空原文件；文件不存在会创建   |
| `a`  | 追加，在文件末尾写入；文件不存在会创建 |
| `rb` | 二进制读                               |
| `wb` | 二进制写                               |

---

## 13.3 读文件

```python
with open("test.txt", "r", encoding="utf-8") as f:
    data = f.read()
    print(data)
```

逐行读取：

```python
with open("test.txt", "r", encoding="utf-8") as f:
    for line in f:
        print(line.strip())
```

---

## 13.4 写文件

```python
with open("test.txt", "w", encoding="utf-8") as f:
    f.write("hello\n")
    f.write("world\n")
```

追加文件：

```python
with open("test.txt", "a", encoding="utf-8") as f:
    f.write("new line\n")
```

---

## 14. 异常处理

## 14.1 基本格式

```python
try:
    x = int(input("请输入数字："))
    print(10 / x)
except ValueError:
    print("输入的不是整数")
except ZeroDivisionError:
    print("不能除以 0")
else:
    print("没有异常时执行")
finally:
    print("无论是否异常都会执行")
```

---

## 14.2 常见异常

| 异常                | 含义                      |
| ------------------- | ------------------------- |
| `ValueError`        | 值错误，比如 `int("abc")` |
| `ZeroDivisionError` | 除以 0                    |
| `IndexError`        | 下标越界                  |
| `KeyError`          | 字典键不存在              |
| `TypeError`         | 类型错误                  |
| `FileNotFoundError` | 文件不存在                |

---

## 15. 面向对象基础

## 15.1 类和对象

类：模板。  
对象：根据模板造出来的具体东西。

```python
class Student:
    def __init__(self, name, age):
        self.name = name
        self.age = age

    def say_hello(self):
        print("我叫", self.name)

s1 = Student("Tom", 18)
s1.say_hello()
```

---

## 15.2 self 是什么？

`self` 表示当前对象自己。

```python
self.name = name
```

意思是：把传进来的 `name` 保存到当前对象的 `name` 属性里。

---

## 15.3 构造方法 __init__

```python
def __init__(self, name, age):
    self.name = name
    self.age = age
```

创建对象时自动调用：

```python
s = Student("Tom", 18)
```

---

## 15.4 类属性和实例属性

```python
class Student:
    school = "江苏大学"   # 类属性

    def __init__(self, name):
        self.name = name   # 实例属性
```

| 类型     | 属于谁           |
| -------- | ---------------- |
| 类属性   | 所有对象共享     |
| 实例属性 | 每个对象自己拥有 |

---

## 16. 常见内置函数

| 函数          | 作用             |
| ------------- | ---------------- |
| `len()`       | 求长度           |
| `sum()`       | 求和             |
| `max()`       | 最大值           |
| `min()`       | 最小值           |
| `sorted()`    | 排序，返回新列表 |
| `range()`     | 生成整数序列     |
| `type()`      | 查看类型         |
| `int()`       | 转整数           |
| `float()`     | 转小数           |
| `str()`       | 转字符串         |
| `list()`      | 转列表           |
| `dict()`      | 转字典           |
| `set()`       | 转集合           |
| `enumerate()` | 同时获得下标和值 |
| `zip()`       | 并行打包多个序列 |

例子：

```python
lst = [3, 1, 4, 2]

print(len(lst))     # 4
print(sum(lst))     # 10
print(max(lst))     # 4
print(min(lst))     # 1
print(sorted(lst))  # [1, 2, 3, 4]
```

---

## 17. 常见编程题套路

## 17.1 判断素数

素数：只能被 1 和自身整除的大于 1 的整数。

```python
n = int(input("请输入一个整数："))

if n <= 1:
    print("不是素数")
else:
    flag = True
    for i in range(2, n):
        if n % i == 0:
            flag = False
            break

    if flag:
        print("是素数")
    else:
        print("不是素数")
```

优化版：只判断到平方根。

```python
n = int(input("请输入一个整数："))

if n <= 1:
    print("不是素数")
else:
    flag = True
    for i in range(2, int(n ** 0.5) + 1):
        if n % i == 0:
            flag = False
            break

    print("是素数" if flag else "不是素数")
```

---

## 17.2 输出 100 以内所有素数

```python
for n in range(2, 101):
    flag = True
    for i in range(2, int(n ** 0.5) + 1):
        if n % i == 0:
            flag = False
            break
    if flag:
        print(n, end=" ")
```

---

## 17.3 水仙花数

三位水仙花数：每个位数字的立方和等于它本身。

例如：

```text
153 = 1^3 + 5^3 + 3^3
```

代码：

```python
for n in range(100, 1000):
    a = n // 100
    b = n // 10 % 10
    c = n % 10

    if a ** 3 + b ** 3 + c ** 3 == n:
        print(n)
```

拆位技巧：

| 数位 | 写法           |
| ---- | -------------- |
| 百位 | `n // 100`     |
| 十位 | `n // 10 % 10` |
| 个位 | `n % 10`       |

---

## 17.4 反转整数

```python
n = int(input("请输入整数："))
rev = 0

while n > 0:
    digit = n % 10
    rev = rev * 10 + digit
    n //= 10

print(rev)
```

例如：

```text
123 -> 321
```

---

## 17.5 最大公约数

普通写法：

```python
a = int(input())
b = int(input())

m = min(a, b)
for i in range(m, 0, -1):
    if a % i == 0 and b % i == 0:
        print(i)
        break
```

辗转相除法：

```python
a = int(input())
b = int(input())

while b != 0:
    a, b = b, a % b

print(a)
```

---

## 17.6 最小公倍数

```python
a = int(input())
b = int(input())

x, y = a, b
while b != 0:
    a, b = b, a % b

gcd = a
lcm = x * y // gcd
print(lcm)
```

公式：

```text
最小公倍数 = 两数乘积 // 最大公约数
```

---

## 17.7 斐波那契数列

```python
n = int(input("请输入项数："))

a, b = 0, 1
for i in range(n):
    print(a, end=" ")
    a, b = b, a + b
```

输出前 10 项：

```text
0 1 1 2 3 5 8 13 21 34
```

---

## 17.8 冒泡排序

```python
lst = [5, 3, 8, 1, 2]

for i in range(len(lst) - 1):
    for j in range(len(lst) - 1 - i):
        if lst[j] > lst[j + 1]:
            lst[j], lst[j + 1] = lst[j + 1], lst[j]

print(lst)
```

理解：

- 每一轮把当前最大的数“冒”到后面。
- 外层控制轮数。
- 内层控制相邻比较。

---

## 17.9 线性查找

```python
lst = [10, 20, 30, 40]
target = int(input("请输入要查找的数："))

pos = -1
for i in range(len(lst)):
    if lst[i] == target:
        pos = i
        break

if pos == -1:
    print("没找到")
else:
    print("下标是", pos)
```

---

## 17.10 统计列表最大值和下标

```python
lst = [3, 9, 2, 9, 5]
max_value = lst[0]
max_index = 0

for i in range(1, len(lst)):
    if lst[i] > max_value:
        max_value = lst[i]
        max_index = i

print(max_value, max_index)
```

---

## 17.11 输入一行数字，求和

输入：

```text
1 2 3 4 5
```

代码：

```python
nums = input().split()
nums = [int(x) for x in nums]
print(sum(nums))
```

合并写法：

```python
nums = list(map(int, input().split()))
print(sum(nums))
```

---

## 18. 程序阅读题常见坑

## 18.1 变量交换

```python
a = 3
b = 5
a, b = b, a
print(a, b)
```

输出：

```text
5 3
```

---

## 18.2 列表赋值不是复制

```python
a = [1, 2, 3]
b = a
b[0] = 100
print(a)
```

输出：

```python
[100, 2, 3]
```

因为 `a` 和 `b` 指向同一个列表。

正确复制：

```python
b = a.copy()
# 或
b = a[:]
```

---

## 18.3 可变对象和不可变对象

| 类型  | 是否可变 |
| ----- | -------- |
| int   | 不可变   |
| float | 不可变   |
| str   | 不可变   |
| tuple | 不可变   |
| list  | 可变     |
| dict  | 可变     |
| set   | 可变     |

---

## 18.4 函数修改列表

```python
def f(lst):
    lst.append(4)

nums = [1, 2, 3]
f(nums)
print(nums)
```

输出：

```python
[1, 2, 3, 4]
```

列表是可变对象，函数里可以改到原列表。

---

## 18.5 and / or 的短路逻辑

```python
print(0 and 5)   # 0
print(1 and 5)   # 5
print(0 or 5)    # 5
print(1 or 5)    # 1
```

考试如果只考 True / False，可以记：

| 表达式    | 结果                     |
| --------- | ------------------------ |
| `A and B` | A 假就返回 A，否则返回 B |
| `A or B`  | A 真就返回 A，否则返回 B |

---

## 18.6 浮点数精度问题

```python
print(0.1 + 0.2)
```

可能输出：

```python
0.30000000000000004
```

所以不要直接用浮点数判断绝对相等。

```python
abs((0.1 + 0.2) - 0.3) < 1e-9
```

---

## 18.7 默认参数不要用可变对象

错误示范：

```python
def f(x, lst=[]):
    lst.append(x)
    return lst

print(f(1))
print(f(2))
```

输出可能是：

```python
[1]
[1, 2]
```

更安全写法：

```python
def f(x, lst=None):
    if lst is None:
        lst = []
    lst.append(x)
    return lst
```

---

## 19. 高频选择题 / 填空题速记

### 19.1 Python 文件后缀

```text
.py
```

### 19.2 Python 用什么表示代码块？

```text
缩进
```

### 19.3 Python 变量需要提前声明吗？

```text
不需要
```

### 19.4 input 返回什么类型？

```text
str 字符串
```

### 19.5 列表和元组区别？

```text
列表 list 可修改，元组 tuple 不可修改。
```

### 19.6 字典通过什么取值？

```text
键 key
```

### 19.7 集合最大特点？

```text
元素不重复，无序。
```

### 19.8 函数没有 return 返回什么？

```text
None
```

### 19.9 `=` 和 `==` 区别？

```text
= 是赋值，== 是判断是否相等。
```

### 19.10 `/` 和 `//` 区别？

```text
/ 是普通除法，结果通常是小数；// 是整除，取商的整数部分。
```

---

## 20. 机考万能模板

## 20.1 输入一个整数

```python
n = int(input())
```

## 20.2 输入多个整数

```python
nums = list(map(int, input().split()))
```

## 20.3 输入 n 个整数，每个一行

```python
n = int(input())
nums = []
for i in range(n):
    nums.append(int(input()))
```

## 20.4 输入 n 个整数，在一行

```python
n = int(input())
nums = list(map(int, input().split()))
```

## 20.5 遍历列表

```python
for x in nums:
    print(x)
```

## 20.6 带下标遍历

```python
for i, x in enumerate(nums):
    print(i, x)
```

## 20.7 统计频次

```python
d = {}
for x in nums:
    d[x] = d.get(x, 0) + 1
```

## 20.8 排序

```python
nums.sort()
```

或：

```python
nums = sorted(nums)
```

---

## 21. 最容易丢分的地方

### 21.1 忘记类型转换

错误：

```python
a = input()
b = input()
print(a + b)
```

如果输入：

```text
1
2
```

输出：

```text
12
```

正确：

```python
a = int(input())
b = int(input())
print(a + b)
```

---

### 21.2 range 忘记右边取不到

```python
for i in range(1, 10):
    print(i)
```

只能输出 1 到 9，不能输出 10。

如果要 1 到 10：

```python
for i in range(1, 11):
    print(i)
```

---

### 21.3 列表下标越界

```python
lst = [1, 2, 3]
print(lst[3])  # 错误
```

最大下标是 `len(lst) - 1`。

---

### 21.4 字典键不存在

```python
d = {"a": 1}
print(d["b"])  # KeyError
```

安全写法：

```python
print(d.get("b", 0))
```

---

### 21.5 while 死循环

错误：

```python
i = 1
while i <= 5:
    print(i)
```

忘记更新 `i`，会一直循环。

正确：

```python
i = 1
while i <= 5:
    print(i)
    i += 1
```

---

## 22. 考前 30 分钟速背清单

1. `input()` 返回字符串，计算前要 `int()` 或 `float()`。
2. `range(a, b)` 包含 `a`，不包含 `b`。
3. `/` 是普通除法，`//` 是整除，`%` 是取余。
4. `=` 是赋值，`==` 是判断相等。
5. 列表 `list` 可修改，元组 `tuple` 不可修改。
6. 字符串、列表都能索引和切片。
7. 字符串不可修改，列表可以修改。
8. 字典用 `key` 找 `value`。
9. `dict.get(key, 默认值)` 可以避免键不存在报错。
10. 函数没有 `return` 默认返回 `None`。
11. `break` 结束整个循环，`continue` 跳过本轮循环。
12. 文件操作推荐使用 `with open(...) as f:`。
13. `sort()` 改原列表，`sorted()` 返回新列表。
14. `append()` 是在列表末尾添加元素。
15. 判断素数、求和、统计次数、排序、查找是编程题高频。

---

## 23. 练习题：自己检查掌握程度

### 题 1：输出 1 到 100 中所有能被 3 整除的数

```python
for i in range(1, 101):
    if i % 3 == 0:
        print(i)
```

---

### 题 2：输入一个字符串，统计字母 a 出现次数

```python
s = input()
print(s.count("a"))
```

---

### 题 3：输入一行整数，输出最大值、最小值、平均值

```python
nums = list(map(int, input().split()))

print(max(nums))
print(min(nums))
print(sum(nums) / len(nums))
```

---

### 题 4：统计一行字符串中每个字符出现次数

```python
s = input()
d = {}

for ch in s:
    d[ch] = d.get(ch, 0) + 1

print(d)
```

---

### 题 5：判断回文字符串

回文：正着读和反着读一样。

```python
s = input()

if s == s[::-1]:
    print("是回文")
else:
    print("不是回文")
```

---

## 24. 最后建议：怎么复习最有效？

不要只背语法，最好按这个顺序来：

1. 先把 `input / print / if / for / while` 搞熟。
2. 再重点练 `字符串、列表、字典`。
3. 然后背几个常见题模板：素数、水仙花、求和、排序、查找、统计次数。
4. 考前多看程序阅读题，尤其注意变量变化、循环次数、下标范围。
5. 遇到不会的代码，逐行写出变量变化表，基本都能看懂。

---

# 附录：超短版语法表

```python
# 输入
x = int(input())
nums = list(map(int, input().split()))

# 输出
print(x)
print(a, b, sep="-", end=" ")

# 分支
if 条件:
    pass
elif 条件:
    pass
else:
    pass

# 循环
for i in range(n):
    pass

while 条件:
    pass

# 函数
def f(a, b):
    return a + b

# 列表
lst = []
lst.append(1)
lst.sort()

# 字典
d = {}
d[key] = value
d[key] = d.get(key, 0) + 1

# 文件
with open("a.txt", "r", encoding="utf-8") as f:
    data = f.read()
```

---

**一句话记忆：Python 考试最核心就是：输入转类型、循环看范围、列表看下标、字典看键、函数看返回值。**