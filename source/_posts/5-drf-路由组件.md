---
title: 5-drf-路由组件
date: 2018-05-20 21:40:20
tags:
---
# 一 路由Routers

对于视图集ViewSet，我们除了可以自己手动指明请求方式与动作action之间的对应关系外，还可以使用Routers来帮助我们快速实现路由信息。

REST framework提供了两个router

- **SimpleRouter**
- **DefaultRouter**

## 1.1 使用方法

1） 创建router对象，并注册视图集，例如

```python
from rest_framework import routers

router = routers.DefaultRouter()
router.register(r'router_stu', StudentModelViewSet, base_name='student')

```

register(prefix, viewset, base_name)

- prefix 该视图集的路由前缀
- viewset 视图集
- base_name 路由别名的前缀

如上述代码会形成的路由如下：

```python
^books/$    name: book-list
^books/{pk}/$   name: book-detail

```

2）添加路由数据

可以有两种方式：

```python
urlpatterns = [
    ...
]
urlpatterns += router.urls

```

或

```python
urlpatterns = [
    ...
    url(r'^', include(router.urls))
]

```

使用路由类给视图集生成了路由地址

```python
from rest_framework.viewsets import ModelViewSet,ReadOnlyModelViewSet
class StudentModelViewSet(ModelViewSet):
    queryset = Student.objects.all()
    serializer_class = StudentModelSerializer

    def login(self,request):
        """学生登录功能"""
        print(self.action)
        return Response({"message":"登录成功"})


```

路由代码：

```python
from django.urls import path, re_path
from . import views
urlpatterns = [
    ...
]

"""使用drf提供路由类router给视图集生成路由列表"""
# 实例化路由类
# drf提供一共提供了两个路由类给我们使用,他们用法一致,功能几乎一样
from rest_framework.routers import DefaultRouter
router = DefaultRouter()

# 注册视图集
# router.register("路由前缀",视图集类)
router.register("router_stu",views.StudentModelViewSet)

# 把生成的路由列表追加到urlpatterns
print( router.urls )
urlpatterns += router.urls



```

上面的代码就成功生成了路由地址[增/删/改/查一条/查多条的功能]，但是不会自动我们在视图集自定义方法的路由。

所以我们如果也要给自定义方法生成路由，则需要进行action动作的声明。



## 1.2 视图集中附加action的声明

在视图集中，如果想要让Router自动帮助我们为自定义的动作生成路由信息，需要使用`rest_framework.decorators.action`装饰器。

以action装饰器装饰的方法名会作为action动作名，与list、retrieve等同。

action装饰器可以接收两个参数：

- **methods**: 声明该action对应的请求方式，列表传递

- detail

  : 声明该action的路径是否与单一资源对应，及是否是

  ```
  xxx/<pk>/action方法名/
  
  ```

  - True 表示路径格式是`xxx/<pk>/action方法名/`
  - False 表示路径格式是`xxx/action方法名/`

举例：

```python
from rest_framework.viewsets import ModelViewSet
from rest_framework.decorators import action

class StudentModelViewSet(ModelViewSet):
    queryset = Student.objects.all()
    serializer_class = StudentModelSerializer

    # methods 设置当前方法允许哪些http请求访问当前视图方法
    # detail 设置当前视图方法是否是操作一个数据
    # detail为True，表示路径名格式应该为 router_stu/{pk}/login/
    @action(methods=['get'], detail=True)
    def login(self, request,pk):
        """登录"""
        ...

    # detail为False 表示路径名格式应该为 router_stu/get_new_5/
    @action(methods=['put'], detail=False)
    def get_new_5(self, request):
        """获取最新添加的5个学生信息"""
        ...

```

由路由器自动为此视图集自定义action方法形成的路由会是如下内容：

```python
^router_stu/get_new_5/$    name: router_stu-get_new_5
^router_stu/{pk}/login/$   name: router_stu-login

```



## 1.3 路由router形成URL的方式

1） SimpleRouter

![SimpleRouter](https://tva1.sinaimg.cn/large/007S8ZIlgy1ggghbmrvbij31940gwdj7.jpg)

2）DefaultRouter

![DefaultRouter](https://tva1.sinaimg.cn/large/007S8ZIlgy1ggghbqkmx1j318y0j4wii.jpg)

DefaultRouter与SimpleRouter的区别是，DefaultRouter会多附带一个默认的API根视图，返回一个包含所有列表视图的超链接响应数据。
