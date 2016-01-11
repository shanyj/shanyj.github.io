---
layout: post
title:  "Django rest_framework 外键&perm"
date:   2015-12-01 20:51:36
categories: Django
excerpt: Django rest_framework 外键&perm
---

* content
{:toc}


## 序

最近对产品api进行rest改造完毕，就记录下Django中rest-framework的一些基本用法

---

### 外键

 * 外键使用
 <pre><code>
    class Suser(models.Model):
        name=models.CharField(max_length=100)
        ok=models.CharField(max_length=100)
    class Snippet(models.Model):
        created = models.DateTimeField(auto_now_add=True)
        title = models.CharField(max_length=100, blank=True, default='')
        code = models.TextField()
        suser=models.ForeignKey('Suser',related_name='snippet')   #注意related_name要和序列化中的一样
        class Meta:
            ordering = ('created',)
 </code></pre>

 <pre><code>
    class SuserSerializer(serializers.ModelSerializer):
        snippet = serializers.PrimaryKeyRelatedField(many=True, queryset=Snippet.objects.all())   #在querset中找存在外键是该user的，相当于user.snipprt_set
        class Meta:
            model=Suser
            fields=('name','ok','snippet')

    class UserList(generics.ListAPIView):
        queryset = Suser.objects.all()
        serializer_class = SuserSerializer
 </code></pre>

 * 显示外键信息
 <pre><code>
    class SnippetSerializer(serializers.ModelSerializer):
        suser=serializers.ReadOnlyField(source='suser.ok')  #注意有source
        class Meta:
            model = Snippet
            fields = ('id', 'title', 'code','suser')
 </code></pre>

 * 格外存储
 <pre><code>
    class hellolist(generics.ListCreateAPIView):
        queryset = Snippet.objects.all()
        serializer_class = SnippetSerializer
        def perform_create(self, serializer):
            serializer.save(suser=Suser.objects.get(ok='10'))
 </code></pre>

---

### permission

 * 添加permission：

   permission_classes = (permissions.IsAuthenticated,)

 * 自定义permission
 <pre><code>
    class BasePermission(object):
        def has_permission(self, request, view):     #注意有三个参数，是访问所有数据的前提
            return True
        def has_object_permission(self, request, view, obj):  #注意有四个参数，限制单个实例的访问即retrie update delete
            return True
 </code></pre>

 * 在rest页面增加logonin：
 <pre><code>
    url(r'^api-auth/', include('rest_framework.urls',
                               namespace='rest_framework'))
 </code></pre>

---

### 改变存取数据的形式

 * 改变存取数据的形式
 <pre><code>
    class SnippetSerializer(serializers.ModelSerializer):
        suser=serializers.ReadOnlyField(source='suser.ok')
        title=serializers.SerializerMethodField('hello',read_only=True)
        def hello(self,instance):
            return 'huohuohuo'
        class Meta:
            model = Snippet
            fields = ('id', 'title', 'code','suser')
 </code></pre>