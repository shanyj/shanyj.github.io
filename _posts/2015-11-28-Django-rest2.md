---
layout: post
title:  "Django rest_framework apiview"
date:   2015-11-28 21:09:56
categories: Django
excerpt: Django rest_framework apiview
---

* content
{:toc}


## 序

最近对产品api进行rest改造完毕，就记录下Django中rest-framework的一些基本用法

---

### apiview

 * apiview使用
 <pre><code>
class hellolist(APIView):
    def get(self,request):
        sni=Snippet.objects.all()
        serializer=SnippetSerializer(sni,many=True)
        return Response(serializer.data)
    def post(self,request):
        serializer=SnippetSerializer(data=request.data)
        if serializer.is_valid():
            serializer.save()
            return Response(serializer.data,status=status.HTTP_201_CREATED)
        return Response(serializer.errors,status=status.HTTP_500_INTERNAL_SERVER_ERROR)
 </code></pre>

 <pre><code>
class hellodetail(APIView):
    def get_object(self, pk):
        try:
            return Snippet.objects.get(pk=pk)
        except Snippet.DoesNotExist:
            raise Http404
    def get(self,request,pk):
        snippet = self.get_object(pk)
        serializer = SnippetSerializer(snippet)
        return Response(serializer.data)
    def delete(self, request, pk):
        snippet = self.get_object(pk)
        snippet.delete()
        return Response(status=status.HTTP_204_NO_CONTENT)
    def put(self, request, pk, format=None):
        snippet = self.get_object(pk)
        serializer = SnippetSerializer(snippet, data=request.data)
        if serializer.is_valid():
            serializer.save()
            return Response(serializer.data)
        return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)
 </code></pre>

---

### mixin

 * mixin使用
 <pre><code>
class hellolist(mixins.ListModelMixin,mixins.CreateModelMixin,generics.GenericAPIView):
    queryset = Snippet.objects.all()
    serializer_class = SnippetSerializer
    def get(self,request, *args, **kwargs):
        return self.list(request, *args, **kwargs)   //注意为list
    def post(self,request, *args, **kwargs):
        return self.create(request, *args, **kwargs)   //注意为create 不为post！
 </code></pre>

 <pre><code>
class hellodetail(mixins.RetrieveModelMixin,mixins.UpdateModelMixin,mixins.DestroyModelMixin,generics.GenericAPIView):
    queryset = Snippet.objects.all()
    serializer_class = SnippetSerializer
    def get(self, request, *args, **kwargs):
        return self.retrieve(request, *args, **kwargs)
    def put(self, request, *args, **kwargs):
        return self.update(request, *args, **kwargs)
    def delete(self, request, *args, **kwargs):
        return self.destroy(request, *args, **kwargs)
 </code></pre>

---

### genertic

 * genertic使用
 <pre><code>
class hellolist(generics.ListCreateAPIView):
    queryset = Snippet.objects.all()
    serializer_class = SnippetSerializer
 </code></pre>

 <pre><code>
class hellodetail(generics.RetrieveUpdateDestroyAPIView):
    queryset = Snippet.objects.all()
    serializer_class = SnippetSerializer
 </code></pre>