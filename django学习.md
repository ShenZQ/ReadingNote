## 准备工作 ##
#### 安装`virtualenv`
安装这个东东是主要目的，是防止与其他项目的依赖包发生版本冲突  
``` shell
 pip install virtualenv -i https://mirrors.aliyun.com/pypi/simple
```
> 安装过程中曾经踫到依赖包`filelock`更新出错，系统表示无法卸载老的版本，也就无法安装新的版本，进而导致`virtualenv`安装失败，解决办法：   
> ``` shell
> pip install filelock --upgrade --ingore-installed filelock
> ```

#### 创建虚拟环境
``` shell
mkdir djangoProjects
cd djangoProjects
virtualenv django
```

### 安装`django`
```
pip install django -i https://mirrors.aliyun.com/pypi/simple
```

## 创建项目
```
django-admin startproject projectName
```
>  这个命令会自动创建一个名为`projectName`的目录，作为项目所在地，这个名字可以随便改的，在项目的`settings.py`中会生成一个`BASE_DIR`指向项目目录。在这个目录下，还会再生成一个名为`projectName`的目录，里面有项目的设置、路径解析等模块。项目目录下还会生成一个`manage.py`的文件，这个文件在后面用于创建应用、迁移数据库、启动服务等。

## 创建应用
在`manage.py`同目录下，用以下命令创建新的应用
```
python manage.py startapp appName
```
>  这个命令会自动创建一个名为`projectName`的目录，作为项目所在地，这个名字可以随便改的，在项目的`settings.py`中会生成一个`BASE_DIR`指向项目目录。在这个目录下，还会再生成一个名为`projectName`的目录，里面有项目的设置、路径解析等模块。项目目录下还会生成一个`manage.py`的文件，这个文件在后面用于创建应用、迁移数据库、启动服务等。

应用创建好后，要将创建的应用添加的`setting.py`的`INSTALLED_APPS`中，这是一个`list`，一般添加在最后，可以是两种形式，一种是直接写`AppName`，到3.0版本后，也可以写成`Appname.apps.AppnameConfig`，只有添加后，之后创建的`Model`才能通过`makemigrations`和`migrate`自动生成数据库。

## 创建模型Model

## 规划路径

## 编写视图View
视图函数也好，视图类也好，都是操作模型类，取出符合要求的数据，再通过`render`函数，用数据渲染模板最终生成`html`文件，返回到浏览器。所以，对`Model`的操作都要这个部分。  
* 获得一个时间段之间的数据
    ```
    Blogs.objects.fileter(createtime__range(datetime1, datetime2))
    ```
    > `createtime`是`Blogs`的一个`DateTimeField`，字段名加上`__range`就是过滤一个范围之间的数据，相当于`SQL`中的`between`子句。
* 判断数据库是否已有指定的数据，通过try/except来捕获DoesNotExist异常。如:
    ```
    try:
        Blogs.objects.get( fieldname = 'xxx' )
    except Blogs.DoesNotExist:
        # fieldname为xxx 的数据不存在

    ```
* `objects.all()`返回的是一个`QuerySet []`类型，是一个list，可以通过切片来获得其中的几个。如取得前5条数据：`Blogs.objects.all()[:5]`。

## 创建模板Template
模板是一个`.html`的文件，但又不是一个普通的`html`文件，它包含了一些模板的语句，这些语句用`{%    %}`或`{{    }}`包围起来。
1. `{{ var  }}`  引用一个从View传来的变量。之后可以用一个` | `跟多种`filter`，实现各种有用的功能。  
   *  `date`过滤器，如`{{ post.createtime | date: "Y-m-d" }}` 显示的就是如`2020-04-24`的日期。  
   *  ` safe`过滤器，当变量中包含有`html`的标签，而且也想显示出来时，可用此过滤器。可以理解为`innerHTML`。   
        ``` 
        post.content = "<h3>这是一个标题</h3><p>这是一个段落</p>"
        在模板文件中有：  
        <div>{{ post.content | safe }}</div>   
        就是相当于：  
        <div><h3>这是一个标题</h3><p>这是一个段落</p></div>   
        ```
1. `{% extends "base.html" %}`说明本模板继续自`base.html`模板，双引号不要忘了。
2. `{% block blockname %}`.... `{% endblock %}`在基模板中定义一个块，或在继承模板中实现一个块。
3. 关于静态文件的引用。静态文件有`css、js、jpg/png`等文件。3.0版的`Django`在这此有一些变化，主要有4步：
   * 确认在`setting.py`的`INSTALLED_APPS`中包含有`django.contrib.staticfiles`。
   * 确认在`setting.py`中有`STATIC_URL = '/static/'`的配置。
   * 在app目录下创建`static`目录，在此目录下存放静态文件，如`css`文件放在`static/css`目录下，`js `文件放在`static/js`目录下，`jpg/png`文件放在`static/images`目录下。
   * 在模板文件中，引用静态文件之前，增加一行，内容为`{% load static %}`，在引用静态文件的地方，使用`{% static  'path'%}`的指令，如：
        ```
        <link rel="stylesheet" href="{% static 'css/post.css' %}">
        ```
        
