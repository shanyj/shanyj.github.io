---
layout: post
title:  "Django django-oauth-toolkit"
date:   2015-12-09 21:01:59
categories: Django
excerpt: Django django-oauth-toolkit
---

* content
{:toc}


## 序

最近对secfile和其他系统如网络账号进行对接,使用了oauth2.0协议,再次记录一下

---

### 安装

 * 安装
 <pre><code>

#终端
pip install django-oauth-toolkit django-cors-headers

#settings.py
INSTALLED_APPS = (
    'oauth2_provider',
    'corsheaders',
)

MIDDLEWARE_CLASSES = (
    'corsheaders.middleware.CorsMiddleware',
)

CORS_ORIGIN_ALLOW_ALL = True

#urls.py
urlpatterns = patterns(
    url(r'^o/', include('oauth2_provider.urls', namespace='oauth2_provider')),
)
 </code></pre>

 * 创建app

   * http://localhost:8000/o/applications/

---

### 授权&token

 * 授权

   * http://localhost:8000/o/authorize?response_type=code&state=whatever&client_id=syj

 * 生成access_token

   * curl -X POST -d
"code=lrKz7kEXJMtYePDLnKKAp10rQ0m9iZ
&client_id=syj
&client_secret=mnHzgZRzbvT920C25o7FnGY
&grant_type=authorization_code
&redirect_uri=http://localhost:8001/"
http://localhost:8001/o/token/

   * redirect_url要和创建app时的一样

   * code为请求授权返回的授权码

---

### 制作api

 * 制作api
 <pre><code>
from oauth2_provider.views.generic import ProtectedResourceView
class ApiEndpoint(ProtectedResourceView):
    def get(self, request, *args, **kwargs):
        return HttpResponse('Hello, OAuth2!')
 </code></pre>

 * 需要加?access_token=才能正确访问

---

### 替代django user

 * 配置settings
 <pre><code>
AUTHENTICATION_BACKENDS = (
    'oauth2_provider.backends.OAuth2Backend',
    #'django.contrib.auth.backends.ModelBackend'      也可以不注释用来登陆admin
)

MIDDLEWARE_CLASSES = (
    '...',
    'django.contrib.auth.middleware.SessionAuthenticationMiddleware',
    'oauth2_provider.middleware.OAuth2TokenMiddleware',
    '...',
)
 </code></pre>

 * 顺序

   * （1）.当SessionAuthenticationMiddleware存在时必须在OAuth2TokenMiddleware之前

   * （2）.SessionAuthenticationMiddleware不是必须的

   * （3）当OAuth2TokenMiddleware在 AuthenticationMiddleware之前或 AuthenticationMiddleware不存在，则只有token验证

   * （4）OAuth2TokenMiddleware在AuthenticationMiddleware之后，则先账号密码验证，通过的话就没事了，如果没通过在用token

 * login_required

   * 视图保护
 <pre><code>
from django.contrib.auth.decorators import login_required
@login_required()
def secret_page(request, *args, **kwargs):
    return HttpResponse('Secret contents!', status=200)

urlpatterns = patterns(
    url(r'^secret$', 'my.views.secret_page', name='secret'),
)
 </code></pre>

   * curl -H "Authorization: Bearer 123456" -X GET http://localhost:8000/secret

---

### rest联用

 * 配置
 <pre><code>
OAUTH2_PROVIDER = {
    'SCOPES': {'read': 'Read scope', 'write': 'Write scope', 'groups': 'Access to your groups'}
}
REST_FRAMEWORK = {
    'DEFAULT_AUTHENTICATION_CLASSES': (
        'oauth2_provider.ext.rest_framework.OAuth2Authentication',
    )，
    'DEFAULT_PERMISSION_CLASSES': (
        'rest_framework.permissions.IsAuthenticated',
    )
}
 </code></pre>

 * permission_classes
 <pre><code>
from oauth2_provider.ext.rest_framework import TokenHasReadWriteScope, TokenHasScope
api_view里加入
    permission_classes = [permissions.IsAuthenticated, TokenHasReadWriteScope]
    permission_classes = [permissions.IsAuthenticated, TokenHasScope]
 </code></pre>

 * 以password方式建立applications

 * curl -X POST -d "grant_type=password&username=<user_name>&password=<password>"
    -u"<client_id>:<client_secret>" http://localhost:8000/o/token/

   获取token

 * curl -H "Authorization: Bearer <your_access_token>" http://localhost:8000/users/

 * 在authorize时可以添加scope属性,&scope=read

