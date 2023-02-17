Bu projede django ile fullstack bir pizza uygulaması yapacağım.
Standart kurulumu yaptım.

1. Öncelikle bir url konfigüasyonu ile include fonksiyonu kullanarak main'den pizza app'e yönlendirme yapıyorum.

**main/urls.py**

from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('pizzas.urls')),


2. Şimdi buraya **home** page eklemek istiyorum.
**pizzas/views.py**

from django.shortcuts import render

def home(request):
    return render(request, 'pizzas/home.html') 

3. **yukarıda pizzas app'inin altında home.html template'ini render etmesini istiyorum**

**pizzas app'in içinde urls.py** oluşturuyorum ve home view'ini import edip url'e bağlıyorum. Local host 8000 şeklinde url'im geldiğinde home view'ini render edecek. Home view de **home template'ini** döndürecek.

from django.urls import path
from .views import home

urlpatterns = [
    path('', home, name='home'),
]

4. **home template'ini** oluşturuyorum
**pizzas/templates/pizzas/home.html**

{% extends 'pizzas/base.html' %}
{% load static %}

{% block content %}
 

    <div class="homeImage">
        {% if messages %} 
        {% for message in messages %} 
            {% if message.tags == "warning" %}
                <div id="warning" class="message rounded col-md-2">{{ message }}</div>
            {% else %}
                <div id="success" class="message rounded col-md-2">{{ message }}</div>
            {% endif %} 
        {% endfor %} 
        {% endif %}

    </div>

        

{% endblock content %}

5. react'teki component mantığına benzer şekilde **pizzas/templates/pizzas/base.html** dosyası oluşturuyorum. Nasıl reactte proje **index.html** içerisinde çalışıyorsa burada da html template'leri **base.html** içerisinde belirllediğim bloklar arasında çalışacak.

{% load static %}
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">

**bootstrapten alacağım ilk linki buraya ekledim**
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0-alpha1/dist/css/bootstrap.min.css" rel="stylesheet" integrity="sha384-GLhlTQ8iRABdZLl6O3oVMWSktQOp6b7In1Zl3/Jr59b6EGGoI1aFkw7cmDA6j6gD" crossorigin="anonymous">
    <link rel="stylesheet" href="{% static 'css/style.css' %}"/>
    <title>Pizza App</title>
</head>
<body>

**blockların üzerine navbarı koyuyorum**
    {% include "pizzas/navbar.html" %} 
    
    {% block content %}
        
    {% endblock content %}
        
**bootstrapten aldığım ikinci linki buraya ekliyorum**    
    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0-alpha1/dist/js/bootstrap.bundle.min.js" integrity="sha384-w76AqPfDkMBDXo30jS1Sgez6pr3x5MlQ1ZAGC+nuZB+EYdgRZgiwxhTBTkF7CXvN" crossorigin="anonymous"></script>
    <script src="{% static 'js/timeout.js' %}"></script>
</body>
</html>

6. Img, css, js gibi static file'larım için ayarlar yapmam gerekiyor.
**main.settings.py'da**

import os
STATIC_URL = '/static/'
STATICFILES_DIRS = [os.path.join(BASE_DIR, 'static')]

# MEDIA FILES
MEDIA_URL = '/media/'
MEDIA_ROOT = os.path.join(BASE_DIR, 'media')
**base_dir içine static isimli klasör açıp, static file'larımı bunun altına eklyeceğim. Static içinde de css, images ve js klasörleri oluşturdum. Django ararken buraya bakacak**
**yine ihtiyaç olursa base_dir altındaki media klasörünün altına media file'larını ekleyebiliriz**

7. css klasörü içine style.css file'ı oluşturuyorum. Burada yazacağım css'i ilgili html template'inin görmesi için template'te küçük ayar yapmam gerekiyor.
Base.html'nin css'leri okuması için burada link vermem gerekiyor.
**base.hmtl'deki link:**
<link rel="stylesheet" href="{% static 'css/style.css' %}"/>
artık base.html'deki class'ları, id'leri, stle.css'ten şekillendirebilirim.
Yine bu linkleri görmesi için base.html'de en üste

{% load static %}
ekliyorum

8. bootstrap kodlarıyla **navbar.html oluşturuyorum**
**base.html'de blockların üstüne koydum, en üstte olacağı için**

{% load static %}


    <!-- navabar start -->
    <nav class="navbar navbar-expand-lg navbar navbar-white">
        <div class="container-fluid ">
            <a class="navbar-brand alert-warning" href="/">
            <img src="{% static 'images/nadia.jpeg' %}" alt="Hi!" width="80" height="80">
            Nadia's Garden Pizza</a>
            
            <div class="collapse navbar-collapse" id="navbars-host">
                <ul class="navbar-nav ms-auto">       

                    <li class="nav-item"><a class="nav-link" href="#">Pizzas</a></li>
                    
                    {% if user.is_authenticated %}
                        <li class="nav-item"><a class="nav-link" href="{% url 'user_logout' %}">Logout</a></li>
                    {% else %}
                        <li class="nav-item"><a class="nav-link" href="{% url 'user_login' %}">Login</a></li>
                        <li class="nav-item"><a class="nav-link" href="{% url 'register' %}">Register</a></li>
                    {% endif %}

                </ul>
            </div>
    </nav>