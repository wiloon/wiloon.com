---
title: python basic
author: "-"
date: 2013-03-24T02:25:43+00:00
url: python
categories:
  - Python
tags:
  - reprint
---
## python basic

## 查看python的版本

python -V

### ubuntu

sudo apt install python3
sudo apt install -y python3-venv
mkdir python3-env
cd python3-env
python3 -m venv my_env
source my_env/bin/activate

<https://www.digitalocean.com/community/tutorials/ubuntu-18-04-python-3-zh>

### archlinux

```bash
    pacman -S python
```
  
### boolean variable

直接定义a=True/False就行，示例代码：

定义布尔值类型参数a,b，值分别为True,False

a=True

b=False

print a,b

print type(a),type(b)

## python 遍历目录

<http://www.cnblogs.com/vivilisa/archive/2009/03/01/1400968.html>

<http://laocao.blog.51cto.com/480714/525140>

```python
# !/usr/bin/python
  
import os,sys
  
dir = '/home/wiloon/tmp'
  
list = os.listdir(dir)
  
print list

for line in list:

path = os.path.join(dir, line)

print path
```

```python
import os
import sys
// 打开文件，只读
f = open("/root/tmp/ip.txt", "r")
// 读取文件
lines = f.readlines()
// 字符串长度
print(len(lines))
// for 循环
for line in lines:
// 去空格
    line = line.strip()
    command = "ansible '" + line + "' -m shell -a 'systemctl start filebeat'"
    print(command)
    // 等待用户 输入
    value = input("press any key to continue:")
    // 判断字符串相等
    if value == "q":
    // 退出
        sys.exit(0)
        // 执行 shwll 命令
    os.system(command)
```

## python 书

<https://zhuanlan.zhihu.com/p/34378860>

## class

```python
# Student 继承 object 类
class Student(object):
    pass

bart = Student()

class Student1(object):
    # 相当于构造函数
    def __init__(self, name, score):
        self.name = name
        self.score = score

    def print_score(self):
        print('%s: %s' % (self.name, self.score))

    # 类的方法
    # 类内部访问数据的函数
    def get_grade(self):
        if self.score >= 90:
            return 'A'
        elif self.score >= 60:
            return 'B'
        else:
            return 'C'
```

## import

```python
# import module_name
# import 搜索路径 sys.path, 运行文件所在的目录

# 打印 sys.path
import sys; print(sys.path)

# Python 会在 sys.path 和运行文件目录这两个地方寻找包，然后导入包中名为module_name的模块。
# from package_name import module_name

```

相对导入和绝对导入

相对导入 `from . import m4`

<https://zhuanlan.zhihu.com/p/63143493>

内置 dir()函数查看对象的属性

from 模块名 import 语句：

 from 模块名 import 子模块 或 函数 或 类 或 变量：使用函数调用

导入的不是整个模块，而是 import 后面的函数或变量

注：在调用导入的模块函数使，不使用模块名.函数名 而是 直接使用函数名进行调用

<https://blog.51cto.com/u_15309669/3154639>

## 下划线

- 单下划线开头: 单下划线开头的变量或方法只在内部使用。PEP 8中定义了这个约定（PEP 8是最常用的Python代码风格指南。详见PEP 8：“Style Guide for Python Code”。

<https://geek-docs.com/python/python-examples/python-underline-double-underline-and-others.html>

## __name__

<https://zhuanlan.zhihu.com/p/57309137>

## Python模块

## celery

<https://github.com/celery/celery>

celery是一个基于分布式消息传输的异步任务队列，它专注于实时处理，同时也支持任务调度。它的执行单元为任务（task），利用多线程，如Eventlet，gevent等，它们能被并发地执行在单个或多个职程服务器（worker servers）上。任务能异步执行（后台运行）或同步执行（等待任务完成）。

## pip install 命令用于安装包

- -U, --upgrade 更新所有指定的包到最新的可用版本。 依赖项的处理取决于所使用的升级策略

## 数据类型

### 字典 dict

```python
scores = {'语文': 89, '数学': 92, '英语': 93}
print(scores)
# 空的花括号代表空的dict
empty_dict = {}
print(empty_dict)
# 使用元组作为dict的key
dict2 = {(20, 30):'good', 30:'bad'}
print(dict2)
```

<http://c.biancheng.net/view/2212.html>

## 异常处理

```python
def func1():
    # 相当于 java 的  throw new Exception
    raise Exception("--func1 exception--")


def main():
    try:
        func1()
    except Exception as e:
        print e


if __name__ == '__main__':
    main()

```

<https://www.jianshu.com/p/a8cb5375171a>

## None

None表示空，但它不等于空字符串、空列表，也不等同于False

<https://zhuanlan.zhihu.com/p/65193194>

## pickle

pickle 提供了一个简单的持久化功能。可以将对象以文件的形式存放在磁盘上。

pickle 模块只能在python中使用，python中几乎所有的数据类型（列表，字典，集合，类等）都可以用pickle来序列化，

pickle 序列化后的数据，可读性差，人一般无法识别。

pickle.dump(obj, file[, protocol])
　　序列化对象，并将结果数据流写入到文件对象中。参数 protocol 是序列化模式，默认值为 0，表示以文本的形式序列化。protocol 的值还可以是1或2，表示以二进制的形式序列化。

pickle.load(file)
　　反序列化对象。将文件中的数据解析为一个Python对象。
