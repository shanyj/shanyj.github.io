---
layout: post
title:  "Django rest_framework基础&序列化"
date:   2015-11-24 20:23:34
categories: Django
excerpt: Django rest_framework基础&序列化
---

* content
{:toc}


## 序

最近对产品api进行rest改造完毕，就记录下Django中rest-framework的一些基本用法

---

### 安装&配置

 * 安装
 <pre><code>
    pip install djangorestframework
    pip install markdown       # Markdown support for the browsable API.
    pip install django-filter  # Filtering support
 </code></pre>

 * 配置
 <pre><code>
    INSTALLED_APPS = (
        ...
        'rest_framework',
    )

    url(r'^api-auth/', include('rest_framework.urls', namespace='rest_framework'))
        #当需要使用browsable API时添加
 </code></pre>

---

### 简单的示例

 * 简单的示例
 <pre><code>
    from django.conf.urls import patterns, include, url
    from django.contrib.auth.models import User
    from rest_framework import routers, serializers, viewsets

    class UserSerializer(serializers.HyperlinkedModelSerializer):
        class Meta:
            model = User
            fields = ('url', 'username', 'email', 'is_staff')

    class UserViewSet(viewsets.ModelViewSet):
        queryset = User.objects.all()
        serializer_class = UserSerializer

    router = routers.DefaultRouter()
    router.register(r'users', UserViewSet)

    urlpatterns = patterns('',
        url(r'^admin/', include(admin.site.urls)),
        url(r'^rest1/$', include(router.urls)),
    )
 </code></pre>

---

### 序列化

 * 序列化
 <pre><code>
    #model.py
    class Snippet(models.Model):
        created = models.DateTimeField(auto_now_add=True)
        title = models.CharField(max_length=100, blank=True, default='')
        code = models.TextField()
        class Meta:
            ordering = ('created',)
 </code></pre>
 <pre><code>
    #serializers.py
    class SnippetSerializer(serializers.ModelSerializer):
        class Meta:
            model = Snippet
            fields = ('id', 'title', 'code')             #fields决定显示哪些
 </code></pre>
 <pre><code>
    #views.py
    from rest_framework.renderers import JSONRenderer
    def hello(request):
        snippet = Snippet(code='foo = "bar"\n')
        snippet.save()

        serializer = SnippetSerializer(snippet)    #转化为python的列表模式
        print serializer.data
        serializer = SnippetSerializer(Snippet.objects.all(), many=True) #many=true可以转化多个

        content = JSONRenderer().render(serializer.data)    #转化为json,注意JSONRenderer有括号
        print content

        stream = BytesIO(content)               #反序列化，先放在bytesio中
        data=JSONParser().parse(stream)
        serializer1 = SnippetSerializer(data=data)
        if serializer1.is_valid():
            val=serializer1.validated_data
            print val
            serializer1.save()     #会存在数据库中
        else:
            print 'aaaaaaaaaa'
        return HttpResponse(serializer.data)
 </code></pre>


---

### request&response

 *  request.POST  # Only works for 'POST' method.

    request.data  # Works for 'POST', 'PUT' and 'PATCH' methods.

 * 例子
 <pre><code>
    from rest_framework import status
    from rest_framework.decorators import api_view
    from rest_framework.response import Response
    @api_view(['GET', 'POST'])
    def hellolist(request):
         if request.method == 'GET':
            snippets = Snippet.objects.all()
            serializer = SnippetSerializer(snippets, many=True)
            return Response(serializer.data)
         if request.method == 'POST':
            serializer = SnippetSerializer(data=request.data)
            if serializer.is_valid():
                serializer.save()
                return Response(serializer.data, status=status.HTTP_201_CREATED)
            return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)
 </code></pre>

 <pre><code>
    @api_view(['GET', 'PUT', 'DELETE'])
    def hellodetail(request,pk):
        try:
            snippet = Snippet.objects.get(pk=pk)
        except Snippet.DoesNotExist:
            return Response(status=status.HTTP_404_NOT_FOUND)
        if request.method == 'GET':
            serializer = SnippetSerializer(snippet)
            return Response(serializer.data)
        elif request.method == 'DELETE':
            snippet.delete()
            return Response(status=status.HTTP_204_NO_CONTENT)
        elif request.method == 'PUT':
            serializer = SnippetSerializer(snippet, data=request.data)
            if serializer.is_valid():
                serializer.save()
                return Response(serializer.data)
            return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)
 </code></pre>