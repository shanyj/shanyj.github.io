---
layout: post
title:  "Django rest_framework 其他技巧"
date:   2015-12-05 21:01:59
categories: Django
excerpt: Django rest_framework 其他技巧
---

* content
{:toc}


## 序

最近对产品api进行rest改造完毕，就记录下Django中rest-framework的一些基本用法

---

### rest_framework使用

 * 显示api入口
 <pre><code>
    from rest_framework.decorators import api_view
    from rest_framework.reverse import reverse
    @api_view(['GET'])
    def api_root(request, format=None):
        return Response({'users': reverse('user-list', request=request, format=format),
            'snippets': reverse('snippet-list', request=request, format=format)})

    url(r'^$',api_root),
    url(r'^xuliehua/$',hellolist.as_view(),name='snippet-list'),
    url(r'^users/$', UserList.as_view(),name='user-list'),
 </code></pre>

 * 利用html显示实例的某个属性
 <pre><code>
    from rest_framework import renderers
    class SnippetHighlight(generics.GenericAPIView):
        queryset = Snippet.objects.all()
        renderer_classes = (renderers.StaticHTMLRenderer,)      #使用html静态页面来渲染
        def get(self, request, *args, **kwargs):
            snippet = self.get_object()
            return Response('<b>'+snippet.title+'</b>')

    url(r'^hello/(?P<pk>\d+)/highlight/$',SnippetHighlight.as_view()),
 </code></pre>

 *  超链接显示属性or详情
 <pre><code>
    class SuserSerializer(serializers.ModelSerializer):
        snippet = serializers.HyperlinkedRelatedField(many= True,read_only=True,view_name='snippet-detail')
        class Meta:
            model=Suser
            fields=('name','ok','snippet')
 </code></pre>

 <pre><code>
    class SnippetSerializer(serializers.ModelSerializer):
        title=serializers.HyperlinkedIdentityField(view_name='snippet-highlight',format='html')
        class Meta:
            model = Snippet
            fields = ('id', 'title', 'code')
 </code></pre>

 * 使用viewsets
 <pre><code>
        class UserViewSet(viewsets.ReadOnlyModelViewSet):
            queryset = Suser.objects.all()
            serializer_class = SuserSerializer
        class SnippetViewSet(viewsets.ModelViewSet):
            queryset = Snippet.objects.all()
            serializer_class = SnippetSerializer
            permission_classes = (permissions.IsAuthenticatedOrReadOnly,)
            @detail_route(renderer_classes=[renderers.StaticHTMLRenderer])
            def highlight(self, request, *args, **kwargs):
                snippet = self.get_object()
                return Response(snippet.title)

        snippet_list = SnippetViewSet.as_view({
            'get': 'list',
            'post': 'create'
        })
        snippet_detail = SnippetViewSet.as_view({
            'get': 'retrieve',
            'put': 'update',
            'delete': 'destroy'
        })
        snippet_highlight = SnippetViewSet.as_view({
            'get': 'highlight'
        }, renderer_classes=[renderers.StaticHTMLRenderer])
        user_list = UserViewSet.as_view({
            'get': 'list'
        })
        user_detail = UserViewSet.as_view({
            'get': 'retrieve'
        })

        url(r'^xuliehua/$',snippet_list,name='snippet-list'),
        url(r'^hello/(?P<pk>\d+)/$',snippet_detail,name='snippet-detail'),
        url(r'^hello/(?P<pk>\d+)/highlight/$',snippet_highlight,name='snippet-highlight'),
        url(r'^users/$', user_list,name='user-list'),
        url(r'^users/(?P<pk>[0-9]+)/$', user_detail,name='suser-detail'),
 </code></pre>
