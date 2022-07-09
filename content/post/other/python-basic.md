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

Python 3 >=3.4 这些版本的 Python 会一并安装 pip

## commands

```bash
# 打印包版本
pip list
pip install "setuptools<58.0.0"
pip install -r requirements.txt
pip freeze #查看当前安装库版本
#创建 requirements.txt 文件，其中包含了当前环境中所有包及 各自的版本的简单列表
#保持部署相同，一键安装所有包
pip install -r requirements.txt
pip freeze > requirements.txt 
lsvirtualenv    #列举所有的环境
cdvirtualenv    #导航到当前激活的虚拟环境的目录中，相当于pushd 目录
cdsitepackages   # 和上面的类似，直接进入到 site-packages 目录
lssitepackages     #显示 site-packages 目录中的内容
```

## 查看 python 的版本, python version

```bash
python -V
python --version
```

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

## list array

准确来说Python中是没有数组类型的，只有列表(list)和元组（tuple), 数组是numpy库中所定义的，所以在使用数组之前必须下载安装numpy库。 python中的list是python的内置数据类型，list中的数据类不必相同的，而array的中的类型必须全部相同。在list中的数据类型保存的是数据的存放的地址，简单的说就是指针，并非数据，这样保存一个list就太麻烦了，例如list1=[1,2,3,'a']需要4个指针和四个数据，增加了存储和消耗cpu。numpy中封装的array有很强大的功能，里面存放的都是相同的数据类型。

列表（List）

1.列表的特点

列表是以方括号“[]”包围的数据集合，不同成员以“，”分隔。如 L = [1,2,3], 列表a有3个成员。
列表是可变的数据类型【可进行增删改查】，列表中可以包含任何数据类型，也可以包含另一个列表。如： L = [1,2,[3,4]]，列表L有3个成员，最后一个成员为一个列表。
列表可以通过序号（索引）访问其中成员，成员序号从0开始，如：a[0]=1。
列表没有shape，计算列表中成员（元素）的个数，成员以最外层的[ ]中的逗号“，”来分隔，计算方式是len(L)=3, L = [1,2,[3,4]] ，没有数组中的a.shape操作。
空列表（0个元素的列表）：L=[], 一个元素的列表：L=[1], 多个元素的列表L=[1,2,3]

元组（Tuple）

1.元组的特点

元组是以圆括号“()”包围的数据集合,括号（）可以省略，不同成员（元素）以逗号“,”分隔，如：T=（1，2,3）。
元组是不可变序列，即元组一旦创建，元组中的数据一旦确立就不能改变，不能对元组中中的元素进行增删改操作，因此元组没有增加元素append、修改元素、删除元素pop的相关方法，只能通过序号（索引）访问元组中的成员,元组中的成员的起始序号为0，如：T[0]=1, T=（1,2,3）。
元组中可以包含任何数据类型，也可以包含另一个元组，如：T=（1,2,3，('a','b')）
空元组（没有元素的元组）：T=（），含1个元素的元组：T=（1，），注意有逗号,多个元素的元组：T=（1,2,3）
任意无符号的对象，以逗号隔开，默认为元组

<https://zhuanlan.zhihu.com/p/210779471>

## python 虚拟环境

- PyPA：指 Python Packaging Authority，一个维护 Python 打包相关项目的小组，相关项目具体见 <https://github.com/pypa>。
- pip：Python 包安装器。
- virtualenv：Python 虚拟环境管理工具。
- venv：Python 标准库内置的虚拟环境管理工具，Python 3.3 加入，Python 3.5 开始作为管理虚拟环境的推荐工具，用法类似 virtualenv。如果你使用 Python 3，推荐使用 venv 来替代 virtualenv。

```bash
# 创建运行环境
python -m venv env0
# 激活环境
source env0/bin/activate
# 退出环境
deactivate
 
```

删除环境
没有使用virtualenvwrapper前，可以直接删除venv文件夹来删除环境

## Virtualenvwrapper

Virtaulenvwrapper是virtualenv的扩展包，用于更方便管理虚拟环境，它可以做： - 将所有虚拟环境整合在一个目录下 - 管理（新增，删除，复制）虚拟环境 - 快速切换虚拟环境

```bash
# 安装
# on macOS / Linux
pip install --user virtualenvwrapper

echo "source virtualenvwrapper.sh" >> ~/.zshrc
source ~/.zshrc

#创建虚拟环境
# on macOS/Linux:
mkvirtualenv --python=python3.6 env0

workon #列出虚拟环境列表
workon [venv] #切换环境

 退出环境
deactivate
# 删除环境
rmvirtualenv venv
```

## pip install python-ldap failed due to cannot find -lldap_r

<https://github.com/python-ldap/python-ldap/issues/432>

```bash
cat > /usr/lib64/libldap_r.so << EOF
INPUT ( libldap.so )
EOF
```

## RuntimeError: populate() isn't reentrant

This is caused by a bug in your Django settings somewhere. Unfortunately, Django's hiding the bug behind this generic and un-useful error message.

To reveal the true problem, open django/apps/registry.py and around line 80, replace:

raise RuntimeError("populate() isn't reentrant")
with:

self.app_configs = {}
This will allow Django to continue loading, and reveal the actual error.

<https://stackoverflow.com/questions/27093746/django-stops-working-with-runtimeerror-populate-isnt-reentrant>

ImportError: libcrypt.so.1: cannot open shared object file: No such file or directory

```bash
sudo pacman -S libxcrypt-compat

```
