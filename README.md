# django-blog
#####简介
Django是一款开源的web框架，使用python语言，采用mvc架构，主要用来构建CMS(内容管理系统)。

---
##### 创建web工程
Django工程包含两个部分
1. 公开网站让所有人都可以访问
2. 管理员网站让你可以进行修改

下面我们就开始创建web工程吧
###### 确保Django的安装
```
python -m django --version
```
在命令行中输入上述代码，如果你的电脑中已经安装django，那么会打印你的django的版本号，如果没有安装，会打印No module named django。这个时候你就需要去安装django了，这里对安装django的安装就不再赘述了。

---
*注意*
1. 如果你的python版本是3以下，命令行输入的命令如上都是：python开头；如果你的python版本是3以上，输入命令的时候就是python3开头，如上面的命令就应该是python3 -m django --version。以下的命令均如此
2. 另外要注意django版本和python版本的支持

---
###### 创建项目工程
进入工程目录下输入以下命令，在这里我使用的项目名称为blog，以下出现blog的地方都可以改为你的项目名。
```
django-admin startproject blog
```
执行该代码之后你会在该目录下有一个blog目录，具体层级数如下：
```
blog/                        //项目根目录，用来存放django项目，可以随便修改名字，对整个项目无影响
    manage.py                //和django项目进行交互的命令行功能
    blog/                    //真实的django项目目录，相当于一个模块
        __init__.py          //空文件，代表一个python包
        settings.py          //django项目的相关配置
        urls.py              //django项目的URL声明
        wsgi.py              //wsgi兼容web服务器的入口，一般不进行修改
```
上面的这么些文件，其实在搭建简单的blog项目，只需要用到settings.py和urls.py

这个时候我们就可以启动服务器了
```
python manage.py runserver
```
这时我们可以在命令行中看见
Starting development server at http://127.0.0.1:8000/
然后打开网页，就可以发现it works出现在眼前。

runserver后面可以接ip以及端口，如下都是可以的
```
python manage.py runserver 8008//http://127.0.0.1:8008
python manage.py runserver 120.0.0.1:8008//http://120.0.0.1:8008 
//当然这个ip是不能分配的，一般默认就行
```
说了这么多，这只是创建了一个项目，接下来我们要创建我们自己的app
###### 创建app
```
python manage.py startapp myblogs
```
运行上面的命令，你会发现当前目录会出现myblogs文件夹，不错这就是我们创建的myblogs app下面我们来看一下其目录结构
```
myblogs/
    __init__.py
    admin.py             //admin管理员界面的相关配置
    apps.py              //
    migrations/          //数据库存放位置，通过相关命令进行数据迁移
        __init__.py
    models.py            //数据模型
    tests.py             //测试用例
    views.py             //界面视图
```
###### 建立视图
myblogs/views.py
```
from django.http import HttpResponse

def index(request):
    return 'Hello World!' 
```
我们只是简单的输出hello world，注意views中的界面应该传入一个参数，默认为request。
接下来我们进行url的配置，首先我们在myblogs下新建urls.py文件，这样能够避免在项目下的urls.py文件的臃肿，减少其配置。
myblogs/urls.py
```
from django.conf.urls import url
from . import views

urlpatterns = {
    url(r'^$',view.index,name='index)
}
```
并且修改项目下的urls.py文件
blog/urls.py
```
from django.conf.urls import include,url
from django.contrib import admin

urlpatterns = {
    url(r'^blog',include('blog.urls'))
    url(r'^admin/',admin.site.urls)
}
```

---
*注意*
url()可以传递几个参数
- argument:regex 端口号后面的url,^代表开头匹配，$表示结尾匹配
- argument: view 当Django匹配到了规定的表达式，调用对应的view方法
- argument: name 为url指向明确的含义，尤其是在templates文件夹下

include() 允许指向其他的url配置信息，这样就可以很方便的为多个应用创建各自的url配置

---
###### 数据库设置
blog/settings.py包含DATABASES这一项，可以通过该项设置数据库，首先看一下默认的设置：
```
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.sqlite3',
        'NAME': os.path.join(BASE_DIR, 'db.sqlite3'),
    }
}
```
默认配置使用的ENGINE是sqlite3，我们还可以设置为其他的如：
'django.db.backends.postgresql'，
'django.db.backends.mysql',
'django.db.backends.oracle
其NAME就是数据库的绝对路径，由于django内置了sqlite3，如果使用该数据库，就可以不用进行设置。

###### 创建数据模型
myblogs/models.py
```
from django.db import models

class Article(models.Model):
    title = models.CharFiled(max_length=32, defalut='title)
    content = models.TextFiled(null = True)
    pub_time = models.DateTimeFiled(auto_now = True)
```
该模型中创建了三个参数，title，content，pub_time，分别设定不同的默认值。
最后别忘了在blog/settings.py中INSTALLED_APPS添加上我们创建的App,结果如下
```
INSTALLED_APPS = [
    'django.contrib.admin',             //管理员站点
    'django.contrib.auth',              //验证系统
    'django.contrib.contenttypes',      //content type Framework
    'django.contrib.sessions',          //会话Framework
    'django.contrib.messages',          //消息Framework
    'django.contrib.staticfiles',       //静态文件管理Framework
    'myblogs'                           //自己创建的App
]
```
###### 数据迁移
```
python manage.py makemigrations myblogs
```
执行该行命令，主要是告诉django，你已经改变了models，需要重新加载migration，其会在myblogs/migrations文件夹下创建0001_initial.py文件，我们可以通过命令行进行查看
```
python manage.py sqlmigrate myblogs 0001
```
此时命令行会打印数据库创建的代码
最后需要执行migrate 来讲models的改变同步到数据库
```
python manage.py migrate
```
###### 创建admin账户
```
python manage.py createsuperuser
```
然后输入用户名，密码以及邮箱就可以了，注意喽，这里的密码不能设置的太简单。一般设置你自己或者项目的名称就可以了。

然后启动服务器
```
python manage.py runserver
```
就可以在浏览器中通过http://127.0.0.1:8000/admin/ 访问我们的admin账户了，但是你会发现admin账户中没有刚才创建在models.py创建的Article对象，怎么办呢？我们可以通过在admin中对该对象进行注册就可以了
myblogs/admin.py
```
from django.contrib import admin

from .models import Article
admin.site.register(Article)
```
这个时候刷新admin，你就会发现Article一栏了，这个时候你就可以进行Article，但是你会发现编辑完了之后，显示的并不是我们编写的title，这是怎么回事呢？我们需要修改一下admin.py
```
class ArticleAdmin(admin.ModelAdmin):
    list_display = ('title', 'content', 'pub_time')

admin.site.register(Article,ArticleAdmin)
```
定义一个ArticleAdmin类，里面包含一个元组list_display，包含我们article的所有属性，然后注册上，就可以了。当然这个元组中你可以只包含你所想要添加的属性。当然也可以定义list_filter，然后管理员界面我们就可以根据相应的属性进行筛选。

###### 定义界面
既然我们做的是博客网站，那么少不了页面了，那么我们应该有哪些界面呢？至少我们应该有主页面，详情页以及编辑界面这三个界面。
首先定义url，先看一下我定义的结果吧
myblogs/urls.py
```
#/blog/
url(r'^$',views.index),
#/blog/5/
url(r'^(?P<article_id>[0-9]+)$', views.article_page, name='article'),
#/blog/edit/5/
url(r'^edit/(?P<article_id>[0-9]+)$', views.edit_page, name='edit_page'),
```
在每个url上面都给了示例，是不是有看不懂，?P<article_id>[0-9]+这一串是什么东西，首先?P<article_id>是决定匹配模式的名字，在第二个URL中就是后面数字所代表的名字，[0-9]+匹配至少一个以上的数字，

url定义好了，那么定义views
myblogs/views.py
```
def index(request):
    articles = models.Article.objects.all()
    return render(request, 'blog/index.html', {'articles': articles})

def article_page(request, article_id):
    article = models.Article.objects.get(pk=article_id)
    return render(request, 'blog/article_page.html', {'article': article})

def edit_page(request, article_id):
    article = models.Article.objects.get(pk=article_id)
    return render(request, 'blog/edit_page.html', {'article': article})
```
是不是很简单，all()可以获得所有的article，如果你不想获得所有的article，并且按照某个顺序的话，可以如下设置：
```
#按照pub_time递减(-)，取前10个数据
articles = models.Article.objects.order_by('-pub_time')[:10]
```
get()传递pk，也就是primary key就可以得到该对象。

现在来编写html模板，我们需要在myblogs下建立templates/myblogs/文件夹
然后编写index.html网页:
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<h1>我的博客</h1>
    {% for a in articles %}
        <a href="{% url 'blog:article' a.id %}">{{ a.title }}</a>
        <br/>
    {% endfor %}
</body>
</html>
```
---
这里有两个语法 
{% %} 中间包含的是语法，
- {% for a in articles %}是for循环，而每一个for循环都必须有end{% endfor %}，if也是一样。
- {% url 'blog:article' a.id %}这个语法包含三个参数，url表明创建一个url，'blog:article'表明指向的是name，冒号前面是在项目下我们blog/urls.py的name='blog'指向的url，冒号后是前一个url指向的app下name='article'指向的url，这样就确定了我们指向的url。第三个参数a.id是我们正则匹配的值

{{ }}中间包含的是数据引用

---
其他的页面也是如此。

你可能会问，那么我们的css,js文件放哪呢？
我们在myblogs下创建static/myblogs/文件夹，如果有图片也可在该文件夹下创建image/文件夹来存放你图片。

现在基本上就已经讲完了，想学到更多知识请看官方网站https://docs.djangoproject.com/