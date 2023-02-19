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
 
 **login ve logout olduğunda for döngüsüyle flash şeklinde mesajlar veriyor.**
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

style.css'de background image'i

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

9. AuthenTication işlemlerine başlıyorum(**login, logout, register**)
**users** app'i oluşturuyorum ve settings.py'da installed apps kısmına ekliyorum

**main url'de** yönlendirme yapıyorum

urlpatterns = [
    ...,
    ...,
    path('users/', include('users.urls')),
]

**users/urls.py** ekliyorum

from django.urls import path
from .views import register, user_login, user_logout

urlpatterns = [
    path("register/", register, name="register"),
    path("login/", user_login, name="user_login"),
    path("logout/", user_logout, name="user_logout"),
]

10. **register'dan** başlayacağım, register view'ini views.py'a ekliyorum

from django.shortcuts import render, redirect
from django.contrib.auth.forms import UserCreationForm
from .forms import UserForm, LoginForm

def register(request):
    # form = UserCreationForm()
    form = UserForm() **artık UserCreationForm'dan inherit ettiğim userform'u kullanıyorum**
**istek post ile form oluştur, form geçerli ise kaydet**    
    if request.method == 'POST':
        # form = UserCreationForm(request.POST)
        form = UserForm(request.POST)
        if form.is_valid():
            user = form.save()
            login(request, user)
 **burada kullanıcı register olduğunda login de oluyor**           
            return redirect('home')
 **formu html'ye context ile gönderiyorum**           
    context = {
        "form": form
    }
    return render(request,'users/register.html', context)

**users/templates/users** klasörü oluşturuyorum. bunun altında **register.html** dosyası ekliyorum, navbara da register linki ekledim

{% extends 'pizzas/base.html' %}


{% block content %}
<div class="col-lg-4 mx-auto p-0 shadow mt-5">
    <div class="alert alert-warning text-center p-1">
        <h2>Registration</h2>
    </div>
    <div class="p-4 d-flex align-items-center ">

**view'den gönderdiğim formu burada yakalıyorum**
        <form action="" method="POST"> 
**güvenlik açıısndan csrf (Cross-site request forgery) kullanıyorum**
            {% csrf_token %} 
            {{ form }}
            <hr>
            <input type="submit" value="Register" class="btn btn-success">
        </form>
    </div>
</div>
{% endblock content %}

11. djangonun hazır formu üzerinde düzenleme yapmak için **users/templates/users/forms.py** oluşturuyorum. 

**forms.py**
from django.contrib.auth.forms import UserCreationForm, UsernameField, AuthenticationForm
from django.contrib.auth.models import User
from django import forms
from django.contrib.auth import password_validation

**usercreationform'dan inherit ederek bir userform oluşturuyorum.**

class UserForm(UserCreationForm):
    
    # class Meta:
    #     model = User
    #     fields = ('username', 'email', 'password1', 'password2')
    
    username = UsernameField(
        label=(""),
        widget=forms.TextInput(attrs={"autofocus": True,'class' : "rounded border border-warning form-control shadow-lg m-2", "placeHolder" :"username"})
        )
**autofocus ile imleç otomatik olarak üzerinde bulunuyor. class'tan da css özelliği veriyorum**
    
    password1 = forms.CharField(
        label=(""),
        widget=forms.PasswordInput(attrs={'class' : "rounded border border-warning form-control shadow-lg m-2", "placeHolder" :"password"}),
        help_text=password_validation.password_validators_help_text_html(),
    )
    
    password2 = forms.CharField(
        label=(""),
        widget=forms.PasswordInput(attrs={'class' : "rounded border border-warning form-control shadow-lg m-2", "placeHolder" :"Password confirmation"}),
        help_text=("* Enter the same password as before, for verification."),
    )
**REGISTER FORM İLE İLGİLİ AÇIKLAMA**
3 farklı yöntemle yaptım:
Birincisinde direkt djangonu sbize sunduğu usercreationform ile yaptım.
İkincisinde, sercreationform'dan inherit ederek serializer gibi basitçe class meta altındaki başlıklarla gerçekleştirdim.
Sonuncuda ise usercreationform'dan inherit ederek detaylı şekilde formların fieldlarını kendim belirleyerek set etmiş oldum.

12. **login** işlemine geçiyorum
**views.py**
from .forms import UserForm, LoginForm
from django.contrib.auth import login, logout, authenticate
from django.contrib import messages


def user_login(request):
    form = LoginForm()
    
    if request.method == 'POST':
        form = LoginForm(request, data=request.POST)
        if form.is_valid():
            user = form.get_user()
            login(request, user)
            messages.success(request, 'You are now logged in') **burada flash şeklinde msj veriyor**
            return redirect('home')
        
    context = {
        "form": form
    }
    return render(request,'users/login.html', context)

**users/templates/users/login.html**
{% extends 'pizzas/base.html' %}


{% block content %}

<div class="col-lg-2 mx-auto p-0 shadow mt-5 ">

    <div class="alert alert-warning text-center">
        <h2>Login</h2>
    </div>

    <div class="p-4 d-flex align-items-center ">
        
        <form action="" method="POST">
            {% csrf_token%} 
            {{ form }}
            <hr>
            <input type="submit" value="LOGIN" class="btn btn-success">
        </form>

    </div>
</div>

{% endblock content %}

**forms.py**
from django.contrib.auth.forms import UserCreationForm, UsernameField, AuthenticationForm

class LoginForm(AuthenticationForm):
    
    username = UsernameField(widget=forms.TextInput(attrs={"autofocus": True, 'class' : "rounded border border-warning form-control shadow-lg m-2", "placeHolder" :"username"}))
    password = forms.CharField(
        label=("Password"),
        widget=forms.PasswordInput(attrs={'class' : "rounded border border-warning form-control shadow-lg m-2", "placeHolder" :"password"}),
    )

**urls.py**
yukarıda yazıldı

13. **LOGOUT**
Burada bir logout template'ine ihtiyacım yok. direkt view yazacağım. Djangonun logout fonksiyonu var, bu beni logout edip home sayfasına yönlendirecek.

**views.py**
from django.contrib import messages

def user_logout(request):
    logout(request)
    messages.warning(request,'logout succesfully') **burada flash olarak mesaj veriyor**
    return redirect('home')

**urls.py**
yukarıda yazıldı

14. **Home sayfasına background image ekliyorum**
home.html yukarıdaydı

**style.css'de**

.homeImage {
    background-image: url('../images/nadiasgarden.jpg');
    height: calc(100vh - 106px);
    background-size: cover;
    background-position: center;
}

15. **login ve logout olduğunda flash şeklinde mesajlar veriyor**
kodlarını yukarıda yazdım, **style.css'de** de mesajların şeklini düzenledim:

#warning {
    margin-top: 5px;
    background-color: lightcoral;
    color: aliceblue;
    margin: auto;
    height: 40px;
    text-align: center;
}

#success {
    margin-top: 5px;
    background-color: darkseagreen ;
    color:rgb(231, 200, 158);
    margin: auto;
    height: 40px;
    text-align: center;
}
**mesajlar otomatik olarak belirli bir süre sonra kaybolsun istiyorum, bunu da JS ile yapıyorum.**


**base.html'e js tagi ekliyorum**

<script src="{% static 'js/timeout.js' %}"></script>

**static/js**
let element = document.querySelector('.message');

setTimeout(function () {
  element.style.display = 'none';
}, 3000);

**AUTHENTICATION'DAN SONRA PIZZA ORDER KISMINA GEÇİYORUM. BURADA PIZZALARIN ÖZELLİKLERİNİ CARD YAPISINDA LİSTELEYEN SAYFAM OLACAK. TIKLAYINCA O PIZZA DAHA DETAYLI ŞEKİLDE GÖSTERİLECEK VE ORADA ORDER BUTONUNA TIKLAYINCA BİZİ PIZZA IMAGE, INFO, UPDATE VE DELETE BUTONLARININ OLDUĞU ORDER SAYFAMIZA YÖNLENDİRECEK**
**PNG'DE ŞEKİL ÇİZİLDİ**
**modellerimi çiziyorum. (models.png)**

16. **Modellerimi** oluşturmaya başlıyorum.

**pizzas/models.py**

from django.db import models
from django.contrib.auth.models import User

class Topping(models.Model):
    name = models.CharField(max_length=100)
    
    def __str__(self):
        return self.name
    
    
class Pizza(models.Model):
    name = models.CharField(max_length=100)
**bir pizzada birden çok topping olabilir, aynı zamanda bir topping birden çok pizzada seçilebilir, (manytomany)**
    toppings = models.ManyToManyField(Topping) 
    image = models.ImageField(upload_to='pizza_pics', blank=True, null=True)
    price = models.DecimalField(max_digits=10, decimal_places=2)
    
    def __str__(self):
        return self.name

Size = (
    ('S', 'Small'),
    ('M', 'Medium'),
    ('L', 'Large'),
)

class Order(models.Model):
    pizza = models.ForeignKey(Pizza, on_delete=models.CASCADE)
    user = models.ForeignKey(User, on_delete=models.CASCADE)
    size = models.CharField(max_length=100, choices=Size, default="M")
    quantity = models.IntegerField(default=1)
    
    def __str__(self):
        return f"{self.user.username} ordered {self.pizza.name}"