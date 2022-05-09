---
title: Python学习
tags:
  - [网络]
  - [Python]
categories: [网络]
date: 2020-06-25 20:08:49
---
<font face="微软雅黑"> </font>
<center> </center>

<!-- more -->

# 参考资料
1. [Python-100-Days](https://github.com/jackfrued/Python-100-Days)
2. [data science cheat sheets](https://www.datacamp.com/community/data-science-cheatsheets)

3. [Improving Your Python Productivity](https://mp.weixin.qq.com/s/h7yO9l3zwPMMmoer8TGLNA)

4. https://learnxinyminutes.com/docs/python/

5. [Streamlit — The fastest way to build data apps in Python](https://github.com/streamlit/streamlit)

# 基础知识
## class和instance
[详细解读Python中的__init__()方法](https://www.cnblogs.com/insane-Mr-Li/p/9758776.html)

Python解释器先使用类方法 `__new__`为类分配空间，并返回类实例。之后调用该类的`__init__()`方法初始化。

class是创建instance的模板，instance则是具体的class。

self中存贮的是instance变量和方法的属性，可以理解为一个dict。self使得class的成员之间能够互相调用，实现封装。

## method
method和instance绑定，各个instance是相互独立的，互不影响。

method可以直接访问class内部的成员，而function不能。method的第一个参数必须为self。

### 实例方法、类方法、静态方法

1. 实例方法：instance传递给参数self。即与第一个参数绑定。
2. 类方法：class -> self 。可通过类或实例调用。
3. 静态方法： 不需要传递class或instance。类似普通函数，区别在于静态方法可以通过class或instance调用。

### unbound method

```
a = A()

a.func()    # bound
A.func(a)   # unbound, unbound method must be called with instance as first argument

```

## 关键字

要引用并修改全局变量必须加上global关键字。

## 生成器generator

1. yeild把函数变为generator，可使用for进行迭代。
2. 生成器函数启动：需使用next(iter_func)/iter_func.send(None)，for迭代时默认会处理，执行到yeild抛出处并暂停。

3. iterator可节省内存，只占用一个可迭代对象大小。而List保存全部对象，占用内存与成员数量为线性关系。

rev = yeild val

1. yeild抛出val值到函数外，并暂停；
2. send()发送值作为 yeild的值；
3. send()/next() 恢复执行——下一条语句。



```
def gen():
    value=0
    while True:
        receive=yield value
        if receive=='e':
            break
        value = 'got: %s' % receive

g=gen()
print(g.send(None))    
print(g.send('hello'))
print(g.send(123456))
print(g.send('e'))

```

### yeild与协程
https://blog.csdn.net/soonfly/article/details/78361819


yield from iterable本质上等于 for item in iterable: yield item

****


# Web框架
Flask：The Python micro framework for building web applications.
## flask与django
[比较与选择](https://www.zhihu.com/question/20706333)
WSGI（Web Server Gateway Interface），
1. Flask 讲究的是 less is more
2. Django 讲究的是 more is less


## fastapi
自动生成API文档：编写API接口后，你就可以使用符合标准的UI如SwaggerUI，ReDoc等来使用API​​。
其它异步框架，包括：Tornado、Snaic、Quart。

```
pip install fastapi uvicorn
```
main.py:
```
from fastapi import FastAPI

app = FastAPI()


@app.get("/")
def read_root():
    return {"Hello": "World"}

@app.get("/items/{item_id}")
def read_item(item_id: int, q: str = None):
    return {"item_id": item_id, "q": q}
```

```
uvicorn main:app --reload
```
可访问：
网站： http://127.0.0.1:8000
Interactive API docs： http://127.0.0.1:8000/docs
Alternative API docs： http://127.0.0.1:8000/redoc

**Uvicorn**:轻量的 **ASGI**服务, built on uvloop and httptools.


# Django学习
官方教程[初识 Django](https://docs.djangoproject.com/zh-hans/3.0/intro/overview/)


## MVT
Django 采用了 MVT 的软件设计模式，即模型（Model），视图（View）和模板（Template）。
- Model:构建并操作web应用的数据。`models.py`
- View：处理用户请求并返回响应。`views.py`,`urls.py`，Django 中的视图的概念是「一类具有相同功能和模板的网页的集合」。
- Template：提供一个对设计者友好的语法用于渲染向用户呈现的信息.`./templates/app_name/template_file`

## Tutorial-创建投票应用
安装2.2.13版本的django，[官方入门教程](https://docs.djangoproject.com/zh-hans/2.2/contents/)。

Django使用对象关系映射（Object Relational Mapping，简称 `ORM` ）用于实现面向对象编程语言里不同类型系统的数据之间的转换


**创建站点**
```
django-admin startproject mysite
```
startproject 创建了:

```
mysite/
    manage.py
    mysite/
        __init__.py
        settings.py
        urls.py
        wsgi.py

```
- `最外层的 mysite/` 根目录只是你项目的容器， Django 不关心它的名字，你可以将它重命名为任何你喜欢的名字。
- `manage.py`: 管理 Django 项目的命令行工具。
- `里面一层的 mysite/` 目录包含你的项目，是一个纯 Python 包。它的名字就是引用它内部任何东西时需要用到的 Python 包名。 (比如 mysite.urls).
- `mysite/__init__.py`：一个空文件，告诉 Python 这个目录应该被认为是一个 Python 包。
- `mysite/settings.py`：Django 项目的配置文件。
- `mysite/urls.py`：Django 项目的 URL 声明。
- `mysite/wsgi.py`：作为你的项目的运行在 WSGI 兼容的Web服务器上的入口。

运行
```
python manage.py runserver 8080
```
**站点内创建app**
```
python manage.py startapp polls

```
这将会创建一个 polls 目录，它的目录结构大致如下：

```
polls/
    __init__.py
    admin.py  #管理界面
    apps.py
    migrations/
        __init__.py
    models.py #模型定义
    tests.py  #测试工具
    views.py  #视图
```

**数据表**
Python 内置 SQLite，数据保存为一个文件。也可使用其它数据库。
默认开启的某些应用在使用之前需要在数据库中创建一些表。执行以下命令：
```
python manage.py migrate
```
创建管理用户：
```
python manage.py createsuperuser

```
交互式命令：
```
python manage.py shell
```
manage.py 会设置 DJANGO_SETTINGS_MODULE 环境变量，这个变量会让 Django 根据 mysite/settings.py 文件来设置 Python 包的导入路径。



**模板**
`/mysite/polls/templates/polls/index.html`

**css**
`./polls/static/polls/style.css`




## 搭建博客

[Python-100Days-Django](https://github.com/jackfrued/Python-100-Days/tree/master/Day41-55)
[blog教程](https://www.zmrenwu.com/courses/hellodjango-blog-tutorial)


已使用GitHub Pages、LNMP+Wordpress、静态网站托管搭建博客，Django客管理较复杂，无必要学习。

---

## 打包与复用


# 爬虫框架
[Scrapy](https://github.com/scrapy/scrapy)
https://www.runoob.com/w3cnote/scrapy-detail.html

[crawlab](https://github.com/crawlab-team/crawlab)：分布式爬虫管理平台，支持任何语言和框架。


# Pillow 与 OpenCV

Pillow代码

    from PIL import Image
    img=Image.open(path)
    cropped=img.crop(0,0,22,22) #left,upper,right,lower
    cropped.save(path)

OpenCV方法

    import cv2
    image=cv2.imread(path)
    cropped=img[y0:y1,x0:x1]
    cv2.imwrite(path,cropped)

## OpenCV中文路径的处理

**问题描述：**
cv2.imread路径为中文时返回None。

**解决方法：**
读取（先用np.fromfile读取为np.uint8格式，再使用cv2.imdecode解码）：

    cv_img = cv2.imdecode(np.fromfile(file_path,dtype=np.uint8),-1)

写入：

    cv2.imencode('.jpg',img)[1].tofile(file_path)

## 批量读写的实现代码

    #coding=utf-8
    import os
    from cv2 import cv2 as cp #解决在VS中cv2报错的问题
    import numpy as np

    def getAllImages(path):
        #f.endswith('.jpg')|f.endswith('.png')  读取jpg/png格式的文件
    return [os.path.join(path,f) for f in os.listdir(path) if f.endswith('.jpg')]

    def cv_imread(filePath):
        cv_img=cp.imdecode(np.fromfile(filePath,dtype=np.uint8),-1)
        # imdecode读取的是rgb，如果后续需要opencv处理的话，需要转换成bgr，转换后图片颜色会变化
        #cv_img=cv2.cvtColor(cv_img,cv2.COLOR_RGB2BGR) 
        return cv_img
        
    orgpath="C:\\Users\\Administrator\\Desktop\\"
 
    for path in getAllImages(orgpath):
        img=cv_imread(path)
        #print(path+img.shape)
        cropped=img[0:1080,120:1920]
        cp.imencode('.jpg',cropped)[1].tofile(path)



# 批量文件重命名
对you-get下载的列表视频进行批量重命名。
将py程序打包，得到一个简单的文件重命名工具。

## Python代码

    import os

    # path='C:/Users/Administrator/Desktop/****'
    print('Input Path')
    path=input()

    print('输入要替换的词：')
    word1=input()
    print(word1)

    print('替换成：')
    word2=input()
    print(word2)

    # print('输入删除文件名的字符数，从0开始')

    f=os.listdir(path)
    for element in f:
        newname=element[:].replace(word1,word2)  #切片可截取文件名
        try:
            os.rename(os.path.join(path,element),os.path.join(path,newname))
        except:
            print(element+'  File error!!:')

        if  (element==newname )==0:
            print(newname+' Rename Success!!')
    os.system("pause") #实现exe执行后不直接关闭terminal

## 程序打包
[pyinstaller](https://www.pyinstaller.org)

    pip install pyinstaller

    pyinstaller -F Rename.py

得到\dist\Rename.exe

**程序运行：**

双击运行或在Shell中输入可执行文件完整路径名（也能避免terminal直接关闭）。