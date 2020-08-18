---
title: 1-drf-drf入门规范
date: 2018-05-10 19:43:20
tags:
---

##  一  Web应用模式

在开发Web应用中，有两种应用模式：

### 1.1 前后端不分离

![前后端不分离](https://tva1.sinaimg.cn/large/007S8ZIlgy1gggfnzeg2xj31k80smjv1.jpg)

### 1.2 前后端分离

![前后端分离](https://tva1.sinaimg.cn/large/007S8ZIlgy1gggfo3sdg9j31aw0u0jxr.jpg)

## 二 API接口

为了在团队内部形成共识、防止个人习惯差异引起的混乱，我们需要找到一种大家都觉得很好的接口实现规范，而且这种规范能够让后端写的接口，用途一目了然，减少双方之间的合作成本。

通过网络，规定了前后台信息交互规则的url链接，也就是前后台信息交互的**媒介**

Web API接口和一般的url链接还是有区别的，Web API接口简单概括有下面四大特点

- url：长得像返回数据的url链接

  - https://api.map.baidu.com/place/v2/search

- 请求方式：get、post、put、patch、delete

  - 采用get方式请求上方接口

- 请求参数：json或xml格式的key-value类型数据

  - ak：6E823f587c95f0148c19993539b99295
  - region：上海
  - query：肯德基
  - output：json

- 响应结果：json或xml格式的数据

  - 上方请求参数的output参数值决定了响应数据的格式

  - ```python
    # xml格式
    https://api.map.baidu.com/place/v2/search?ak=6E823f587c95f0148c19993539b99295&region=%E4%B8%8A%E6%B5%B7&query=%E8%82%AF%E5%BE%B7%E5%9F%BA&output=xml
    #json格式
    https://api.map.baidu.com/place/v2/search?ak=6E823f587c95f0148c19993539b99295&region=%E4%B8%8A%E6%B5%B7&query=%E8%82%AF%E5%BE%B7%E5%9F%BA&output=json
    {
        "status":0,
      	"message":"ok",
        "results":[
            {
                "name":"肯德基(罗餐厅)",
                "location":{
                    "lat":31.415354,
                    "lng":121.357339
                },
                "address":"月罗路2380号",
                "province":"上海市",
                "city":"上海市",
                "area":"宝山区",
                "street_id":"339ed41ae1d6dc320a5cb37c",
                "telephone":"(021)56761006",
                "detail":1,
                "uid":"339ed41ae1d6dc320a5cb37c"
            }
          	...
    		]
    }
    ```

## 三 接口测试工具：Postman

Postman是一款接口调试工具，是一款免费的可视化软件，同时支持各种操作系统平台，是测试接口的首选工具。

Postman可以直接从[官网：https://www.getpostman.com/downloads/](https://www.getpostman.com/downloads/)下载获得，然后进行傻瓜式安装。

- 工作面板

![img](https://img2018.cnblogs.com/blog/1825659/201911/1825659-20191115214221927-317456262..png)

- 简易的get请求

![img](https://img2018.cnblogs.com/blog/1825659/201911/1825659-20191115214225177-1349141268..png)

- 简易的post请求

![img](https://img2018.cnblogs.com/blog/1825659/201911/1825659-20191115214229410-866523703..png)

- 案例：请求百度地图接口

![img](https://img2018.cnblogs.com/blog/1825659/201911/1825659-20191115214232002-1319748252..png)

## 四 RESTful API规范

![restful](https://tva1.sinaimg.cn/large/007S8ZIlgy1gggfoaosexg30io08xaae.gif)

REST全称是Representational State Transfer，中文意思是表述（编者注：通常译为表征性状态转移）。 它首次出现在2000年Roy Fielding的博士论文中。

RESTful是一种定义Web API接口的设计风格，尤其适用于前后端分离的应用模式中。

这种风格的理念认为后端开发任务就是提供数据的，对外提供的是数据资源的访问接口，所以在定义接口时，客户端访问的URL路径就表示这种要操作的数据资源。

事实上，我们可以使用任何一个框架都可以实现符合restful规范的API接口。

### 4.1 数据的安全保障

- url链接一般都采用https协议进行传输

  注：采用https协议，可以提高数据交互过程中的安全性

### 4.2 接口特征表现

- 用api关键字标识接口url：

  - [https://api.baidu.com](https://api.baidu.com/)
  - https://www.baidu.com/api

  注：看到api字眼，就代表该请求url链接是完成前后台数据交互的

### 4.3 多数据版本共存

- 在url链接中标识数据版本

  - https://api.baidu.com/v1
  - https://api.baidu.com/v2

  注：url链接中的v1、v2就是不同数据版本的体现（只有在一种数据资源有多版本情况下）

### 4.4 数据即是资源，均使用名词（可复数）

- 接口一般都是完成前后台数据的交互，交互的数据我们称之为资源

  - https://api.baidu.com/users
  - https://api.baidu.com/books
  - https://api.baidu.com/book

  注：一般提倡用资源的复数形式，在url链接中奖励不要出现操作资源的动词，错误示范：https://api.baidu.com/delete-user

- 特殊的接口可以出现动词，因为这些接口一般没有一个明确的资源，或是动词就是接口的核心含义

  - https://api.baidu.com/place/search
  - https://api.baidu.com/login

### 4.5 资源操作由请求方式决定（method）

- 操作资源一般都会涉及到增删改查，我们提供请求方式来标识增删改查动作
  - https://api.baidu.com/books - get请求：获取所有书
  - https://api.baidu.com/books/1 - get请求：获取主键为1的书
  - https://api.baidu.com/books - post请求：新增一本书书
  - https://api.baidu.com/books/1 - put请求：整体修改主键为1的书
  - https://api.baidu.com/books/1 - patch请求：局部修改主键为1的书
  - https://api.baidu.com/books/1 - delete请求：删除主键为1的书

### 4.6 过滤，通过在url上传参的形式传递搜索条件

- https://api.example.com/v1/zoos?limit=10：指定返回记录的数量
- https://api.example.com/v1/zoos?offset=10：指定返回记录的开始位置
- https://api.example.com/v1/zoos?page=2&per_page=100：指定第几页，以及每页的记录数
- https://api.example.com/v1/zoos?sortby=name&order=asc：指定返回结果按照哪个属性排序，以及排序顺序
- https://api.example.com/v1/zoos?animal_type_id=1：指定筛选条件

### 4.7 响应状态码

#### 4.7.1 正常响应

- 响应状态码2xx
  - 200：常规请求
  - 201：创建成功

#### 4.7.2 重定向响应

- 响应状态码3xx
  - 301：永久重定向
  - 302：暂时重定向

#### 4.7.3 客户端异常

- 响应状态码4xx
  - 403：请求无权限
  - 404：请求路径不存在
  - 405：请求方法不存在

#### 4.7.4 服务器异常

- 响应状态码5xx
  - 500：服务器异常

### 4.8 错误处理，应返回错误信息，error当做key

```python
{
    error: "无权限操作"
}
```



### 4.9 返回结果，针对不同操作，服务器向用户返回的结果应该符合以下规范

```python
GET /collection：返回资源对象的列表（数组）
GET /collection/resource：返回单个资源对象
POST /collection：返回新生成的资源对象
PUT /collection/resource：返回完整的资源对象
PATCH /collection/resource：返回完整的资源对象
DELETE /collection/resource：返回一个空文档
```

### 4.10 需要url请求的资源需要访问资源的请求链接

```json
# Hypermedia API，RESTful API最好做到Hypermedia，即返回结果中提供链接，连向其他API方法，使得用户不查文档，也知道下一步应该做什么
{
  	"status": 0,
  	"msg": "ok",
  	"results":[
        {
            "name":"肯德基(罗餐厅)",
            "img": "https://image.baidu.com/kfc/001.png"
        }
      	...
		]
}
```

比较好的接口返回

```json
# 响应数据要有状态码、状态信息以及数据本身
{
  	"status": 0,
  	"msg": "ok",
  	"results":[
        {
            "name":"肯德基(罗餐厅)",
            "location":{
                "lat":31.415354,
                "lng":121.357339
            },
            "address":"月罗路2380号",
            "province":"上海市",
            "city":"上海市",
            "area":"宝山区",
            "street_id":"339ed41ae1d6dc320a5cb37c",
            "telephone":"(021)56761006",
            "detail":1,
            "uid":"339ed41ae1d6dc320a5cb37c"
        }
      	...
		]
}
```



## 四 序列化

api接口开发，最核心最常见的一个过程就是序列化，所谓序列化就是把**数据转换格式**，序列化可以分两个阶段：

**序列化**： 把我们识别的数据转换成指定的格式提供给别人。

例如：我们在django中获取到的数据默认是模型对象，但是模型对象数据无法直接提供给前端或别的平台使用，所以我们需要把数据进行序列化，变成字符串或者json数据，提供给别人。

**反序列化**：把别人提供的数据转换/还原成我们需要的格式。

例如：前端js提供过来的json数据，对于python而言就是字符串，我们需要进行反序列化换成模型类对象，这样我们才能把数据保存到数据库中。





## 五 Django Rest_Framework

核心思想: 缩减编写api接口的代码

Django REST framework是一个建立在Django基础之上的Web 应用开发框架，可以快速的开发REST API接口应用。在REST framework中，提供了序列化器Serialzier的定义，可以帮助我们简化序列化与反序列化的过程，不仅如此，还提供丰富的类视图、扩展类、视图集来简化视图的编写工作。REST framework还提供了认证、权限、限流、过滤、分页、接口文档等功能支持。REST framework提供了一个API 的Web可视化界面来方便查看测试接口。

![drf_logo](https://tva1.sinaimg.cn/large/007S8ZIlgy1ggggn3x4saj31fi0g4gph.jpg)

官方文档：https://www.django-rest-framework.org/

github: https://github.com/encode/django-rest-framework/tree/master

### 特点

- 提供了定义序列化器Serializer的方法，可以快速根据 Django ORM 或者其它库自动序列化/反序列化；
- 提供了丰富的类视图、Mixin扩展类，简化视图的编写；
- 丰富的定制层级：函数视图、类视图、视图集合到自动生成 API，满足各种需要；
- 多种身份认证和权限认证方式的支持；[jwt]
- 内置了限流系统；
- 直观的 API web 界面；
- 可扩展性，插件丰富



## 六 环境安装与配置

DRF需要以下依赖：

- Python (2.7, 3.2, 3.3, 3.4, 3.5, 3.6)
- Django (1.10, 1.11, 2.0)

**DRF是以Django扩展应用的方式提供的，所以我们可以直接利用已有的Django环境而无需从新创建。（若没有Django环境，需要先创建环境安装Django）**



### 6.1 安装DRF

前提是已经安装了django，建议安装在虚拟环境

```python
# mkvirtualenv drfdemo -p python3
# pip install django

pip install djangorestframework
pip install pymysql
```



#### 6.1.1 创建django项目

```
cd ~/Desktop
django-admin startproject drfdemo
```

使用pycharm打开项目，设置虚拟环境的解析器，并修改manage.py中的后缀参数。



### 6.2 添加rest_framework应用

在**settings.py**的**INSTALLED_APPS**中添加'rest_framework'。

```python
INSTALLED_APPS = [
    ...
    'rest_framework',
]
```

接下来就可以使用DRF提供的功能进行api接口开发了。在项目中如果使用rest_framework框架实现API接口，主要有以下三个步骤：

- 将请求的数据（如JSON格式）转换为模型类对象
- 操作数据库
- 将模型类对象转换为响应的数据（如JSON格式）





接下来，我们快速体验下四天后我们学习完成drf以后的开发代码。接下来代码不需要理解，看步骤。

### 6.3 体验drf完全简写代码的过程（了解）

#### 6.3.1. 创建模型操作类

```python
class Student(models.Model):
    # 模型字段
    name = models.CharField(max_length=100,verbose_name="姓名")
    sex = models.BooleanField(default=1,verbose_name="性别")
    age = models.IntegerField(verbose_name="年龄")
    class_null = models.CharField(max_length=5,verbose_name="班级编号")
    description = models.TextField(max_length=1000,verbose_name="个性签名")

    class Meta:
        db_table="tb_student"
        verbose_name = "学生"
        verbose_name_plural = verbose_name
```

为了方便测试，所以我们可以先创建一个数据库。

```
create database students charset=utf8;
```

##### 6.3.1.1 执行数据迁移

把students子应用添加到INSTALL_APPS中

![1557023819604](https://tva1.sinaimg.cn/large/007S8ZIlgy1ggggnfes9gj30na07pq50.jpg)



初始化数据库连接

```
安装pymysql
pip install pymysql
```

主引用中`__init__.py`设置使用pymysql作为数据库驱动

```python
import pymysql

pymysql.install_as_MySQLdb()
```



settings.py配置文件中设置mysql的账号密码

```python
DATABASES = {
    # 'default': {
    #     'ENGINE': 'django.db.backends.sqlite3',
    #     'NAME': os.path.join(BASE_DIR, 'db.sqlite3'),
    # },
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': "students",
        "HOST": "127.0.0.1",
        "PORT": 3306,
        "USER": "root",
        "PASSWORD":"123",
    },
}
```



终端下，执行数据迁移。

```
python manage.py makemigrations
python manage.py migrate
```

 

错误列表

```python
# 执行数据迁移 python manage.py makemigrations 报错如下：
```

![1557024349366](https://tva1.sinaimg.cn/large/007S8ZIlgy1ggggnmwcygj30ro08j0xb.jpg)

解决方案：

```python
注释掉 backends/mysql/base.py中的35和36行代码。
```

![1557025991751](https://tva1.sinaimg.cn/large/007S8ZIlgy1ggggnrlrh4j30ri040abp.jpg)



```python
# 执行数据迁移发生以下错误：
```

![1557026113769](https://tva1.sinaimg.cn/large/007S8ZIlgy1ggggnvurajj30rc07v41a.jpg)

解决方法：

backends/mysql/operations.py146行里面新增一个行代码：

![1557026224431](https://tva1.sinaimg.cn/large/007S8ZIlgy1ggggnzzyggj30s704rmz0.jpg)





#### 6.3.2. 创建序列化器

例如，在django项目中创建学生子应用。

```python
python manage.py startapp students
```

在syudents应用目录中新建serializers.py用于保存该应用的序列化器。

创建一个StudentModelSerializer用于序列化与反序列化。

```python
# 创建序列化器类，回头会在试图中被调用
class StudentModelSerializer(serializers.ModelSerializer):
    class Meta:
        model = Student
        fields = "__all__"
```

- **model** 指明该序列化器处理的数据字段从模型类BookInfo参考生成
- **fields** 指明该序列化器包含模型类中的哪些字段，'__all__'指明包含所有字段



#### 6.3.3. 编写视图

在students应用的views.py中创建视图StudentViewSet，这是一个视图集合。

```python
from rest_framework.viewsets import ModelViewSet
from .models import Student
from .serializers import StudentModelSerializer
# Create your views here.
class StudentViewSet(ModelViewSet):
    queryset = Student.objects.all()
    serializer_class = StudentModelSerializer
```

- **queryset** 指明该视图集在查询数据时使用的查询集
- **serializer_class** 指明该视图在进行序列化或反序列化时使用的序列化器

#### 6.3.4. 定义路由

在students应用的urls.py中定义路由信息。

```python
from . import views
from rest_framework.routers import DefaultRouter

# 路由列表
urlpatterns = []

router = DefaultRouter()  # 可以处理视图的路由器
router.register('students', views.StudentViewSet)  # 向路由器中注册视图集

urlpatterns += router.urls  # 将路由器中的所以路由信息追到到django的路由列表中
```

最后把students子应用中的路由文件加载到总路由文件中.

```python
from django.contrib import admin
from django.urls import path,include

urlpatterns = [
    path('admin/', admin.site.urls),
    path("stu/",include("students.urls")),
]

```



#### 6.3.5. 运行测试

运行当前程序（与运行Django一样）

```shell
python manage.py runserver
```

在浏览器中输入网址127.0.0.1:8000，可以看到DRF提供的API Web浏览页面：

![1557027948031](https://tva1.sinaimg.cn/large/007S8ZIlgy1ggggo7yztej30zl0c7gmg.jpg)



1）点击链接127.0.0.1:8000/stu/students 可以访问**获取所有数据的接口**，呈现如下页面：

![1557027878963](https://tva1.sinaimg.cn/large/007S8ZIlgy1ggggobtfhkj30zf0i20u0.jpg)





2）在页面底下表单部分填写学生信息，可以访问**添加新学生的接口**，保存学生信息：

![1557027999506](https://tva1.sinaimg.cn/large/007S8ZIlgy1ggggog4ptxj30yq0hgt9h.jpg)

点击POST后，返回如下页面信息：

![1557028072470](https://tva1.sinaimg.cn/large/007S8ZIlgy1ggggojnhflj30z70ekjsd.jpg)



3）在浏览器中输入网址127.0.0.1:8000/stu/students/5/，可以访问**获取单一学生信息的接口**（id为5的学生），呈现如下页面：

![1557028115925](https://tva1.sinaimg.cn/large/007S8ZIlgy1ggggooinxwj30z80hdabb.jpg)

4）在页面底部表单中填写学生信息，可以访问**修改学生的接口**：

![1557028168350](https://tva1.sinaimg.cn/large/007S8ZIlgy1ggggoujf51j30w50awwet.jpg)

点击PUT，返回如下页面信息：

![1557028208243](https://tva1.sinaimg.cn/large/007S8ZIlgy1ggggoza9rbj30x20bx752.jpg)

5）点击DELETE按钮，可以访问**删除学生的接口**：

![1557028242637](https://tva1.sinaimg.cn/large/007S8ZIlgy1ggggp3eceqj30zt08tt9o.jpg)

返回，如下页面：

![1557028266190](https://tva1.sinaimg.cn/large/007S8ZIlgy1ggggp74kyuj30ze0al3zd.jpg)

## 七 CBV源码分析

```python
# 视图层
from django.shortcuts import render, HttpResponse
from django.views import View
class CBVTest(View):
    # 通过调度(dispatch)分发请求
    def dispatch(self, request, *args, **kwargs):
        pass
        super().dispatch(request, *args, **kwargs)
        pass

    def get(self, request):
        return render(request, 'cbv.html')

    def post(self, request):
        return HttpResponse('cbv post method')
```

```html
<!-- 模板层 -->
<form action="/cbv/" method="post">
    <input type="text" name="usr">
    <button type="submit">提交</button>
</form>
```

```python
# 路由层
from app import views
urlpatterns = [
    url(r'^cbv/', views.CBVTest.as_view()),
]
```

## 八 drf基本使用及request源码分析

### 8.1 APIView的使用

```python
# 1）安装drf：pip3 install djangorestframework
# 2）settings.py注册app：INSTALLED_APPS = [..., 'rest_framework']
# 3）基于cbv完成满足RSSTful规范的接口
```

```python
# 视图层
from rest_framework.views import APIView
from rest_framework.response import Response
user_list = [{'id': 1, 'name': 'Bob'}, {'id': 2, 'name': 'Tom'}]
class Users(APIView):
    def get(self, request, *args, **kwargs):
        return Response({
            'status': 0,
            'msg': 'ok',
            'results': user_list
        })
    def post(self, request, *args, **kwargs):
        # request对formdata，urlencoded，json三个格式参数均能解析
        name = request.data.get('name')
        id = len(user_list) + 1
        user = {'id': id, 'name': name}
        user_list.append(user)
        return Response({
            'status': '0',
            'msg': 'ok',
            'results': user
        })
```

```python
# 路由层
from app import views
urlpatterns = [
    url(r'^users/', views.Users.as_view()),
]
```

## 8.2 APIView和Request对象源码分析

#### 8.2.1 APIView

```python
# as_view()
	# 核心走了父类as_view
	view = super(APIView, cls).as_view(**initkwargs)
    # 返回的是局部禁用csrf认证的view视图函数
    return csrf_exempt(view)
    
# dispatch(self, request, *args, **kwargs)
	# 二次封装request对象
	request = self.initialize_request(request, *args, **kwargs)
    # 自定义request规则
    self.initial(request, *args, **kwargs)
    
# initialize_request(self, request, *args, **kwargs)
	# 原生request封装在request._request
    
# initial(self, request, *args, **kwargs)
	# 认证
	self.perform_authentication(request)
    # 权限
    self.check_permissions(request)
    # 频率
    self.check_throttles(request)
```

#### 8.2.2 Request对象

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1ggggzi3mjxj30hc0t0aga.jpg" alt="image-20200705223439308" style="zoom:33%;" />
