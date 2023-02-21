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
**yine image yüklediğimde kendisi base_dir altında media klasörün oluşturucak ve altına media file'ları eklenecek**

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
**settings.py'daki media ayarları doğrultusunda base dir'de media klasörü açtı ve sonra altına yukarıda belirttiğim gibi pizza pics oluşturdu ve altına pizza imageleri ekledi**

**Önemli not!! Gerçek projelerde imageler db'de saklanmaz, cloud gibi yerlerde saklanır. O durumlarda da oranın adresi verilir**
    price = models.DecimalField(max_digits=10, decimal_places=2)
    
    def __str__(self):
        return self.name

Size = (
    ('S', 'Small'), **sol taraf db'de işlenecek kısım, sağ taraf kullanıcıların göreceği kısım**
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


image ile ilgili işlem yapacaksam **pillow install** etmem gerekiyor.
**pip install Pillow**
pip freeze > requirements.txt
python manage.py makemigrations
python manage.py migrate

**admin panelde görmek için**

**pizzas/admin.py**
from django.contrib import admin
from .models import Topping, Pizza, Order


admin.site.register(Topping)
admin.site.register(Pizza)
admin.site.register(Order)

**imageler ile ilgili main urls.py'da ayarlarımı yapıyorum**

from django.conf import settings
from django.conf.urls.static import static

urlpatterns = [
    ...,
    ...,
    ..., 
] + static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)

**manuel olarak admin panelde pizzaları ve orderları oluşturdum**

17. Pizza page'imi oluşturmaya başlıyorum

**pizzas/views.py**

from django.shortcuts import render, redirect
from .models import Pizza, Order

def home(request):
    return render(request, 'pizzas/home.html')


def pizzas(request):
    pizzas = Pizza.objects.all() **db'deki pizzaları çekiyorum, yukarıda pizza modelini çektim, import ettim**

**pizzaları db'den çektim, değişkene atayıp context paketinin içine koyup template'e gönderdim**    
    context = {
        'pizzas': pizzas 
    }
    return render(request, 'pizzas/pizzas.html', context)

**şimdi endpointi oluşturacağım, endpointe istek attığımda pizzas viewini çalıştıracak, o da pizzas.html'yi render edecek!!!** 

**pizzas/urls.py**
from django.urls import path
from .views import home, pizzas, order_view, my_orders, update_order_view, delete_order_view

urlpatterns = [
    ...,
    path('pizzas/', pizzas, name='pizzas'),
    path('pizzas/<int:id>', order_view, name='order'),

**render edilecek pizzas.html'i oluşturacağım. pizzas.html ismini kullanmam gerek!!**
**navbardaki pizzas butonuna tıkladığımda da aşağıda oluşturacağım pizzas.html sayfasına gitmek istiyorum**
**pizzas/templates/pizzas/navbar.html**
butonun linkine yeni oluşturduğum **pizzas endpointi** koydum

    <li class="nav-item"><a class="nav-link" href="{% url 'pizzas' %}">Pizzas</a></li>

**pizzas.html**

{% extends 'pizzas/base.html' %}


{% block content %}

**flex yapısıyla cardlarımı yerleştiriyorum**
<div class="d-flex justify-content-around flex-wrap mt-5">

**birden çok pizza var, for döngüsüyle listeliyorum**
**bootstrap'ten card yapısı alıyorum**
    {% for pizza in pizzas %} 
**gölgelendirme yapıyorum**
    <div class="card m-5shadow" style="max-width: 640px;">
        <div class="row g-0">
          <div class="col-md-4">
**burada pizzaların imagelerinin url'lerini ekliyorum, height'a 100 vererek cardları tam oturttum,**
            <img src="{{pizza.image.url}}" class="img-fluid rounded-start"  style="height: 100%;"  alt="...">
          </div>
          <div class="col-md-8">
            <div class="card-body">
              <h5 class="card-title">{{pizza.name}}</h5>
              <p class="card-text">
                <ul>
                    <li>
**pizza toppings ayrı bir queryset olduğu için pizza.name, pizza.price gibi ulaşamıyorum**
                        {{pizza.toppings.all |join:", "}}
                    </li>
                    <li>
                      <strong> {{pizza.price}} $</strong> 
                    </li>
            </ul>
             </p>
**login olmamış kullanıcıya order butonu göstermek istemiyorum**
             {% if user.is_authenticated %}
             <p class="card-text"><a href="{% url 'order' pizza.id %}"><button class=" btn btn-warning ms-1">Order</button></a></p>
             {% endif %}
                
            </div>
          </div>
        </div>
      </div>
    {% endfor %}

</div>
{% endblock content %}

18. Butona tıklayınca beni yönlendireceği **order sayfasını** oluşturuyorum

**pizzas/views.py**
**buraya gelen isteğin request ve id ile birlikte gelmesi lazım**

from django.shotcuts import ..., redirect
from .forms import PizzaForm

def order_view(request, id):
    pizza = Pizza.objects.get(id=id)
    form = PizzaForm(request.POST or None)
    if request.method == 'POST':
        if form.is_valid():
            order = form.save(commit=False)
**!!!normalde order=form.save dediysem order modelinin formunu order tablosunda db'ye kaydetmesi gerekir. Ancak commit=false deyince order'ı direkt db'ye kaydetmiyor, create edilmiş gibi yakalayıp değişkene atabiliyorum, db'ye kaydetmeden önce değişiklikler yapıp db'ye öyle kaydedebiliyorum**
            order.pizza = pizza
            order.user = request.user
            order.save()
            return redirect('my_orders') **my_orders sayfasına gönderecek**            
    
    context = {
        'pizza': pizza,
        'form': form
    }
    return render(request, 'pizzas/order.html', context)

**pizzas/urls.py**
pizza orderı bir endpointe bağlıyorum. bunun url'i dinamik olacak, hangi pizzaya tıklanırsa o gelecek.

from .views import ..., ..., order_view, my_orders, update_order_view, delete_order_view

urlpatterns = [
    ...,
    ...,
    path('pizzas/<int:id>', order_view, name='order')

**pizzas/templates/pizzas/order.html**

{% extends 'pizzas/base.html' %}

{% block content %}
<div class="col-lg-3 mx-auto d-flex justify-content-center align-items-center mt-5">

**bootstrapten card yapısını buraya yerleştirdim**
    <div class="card" style="width: 100%;">
        <img src="{{pizza.image.url}}" class="card-img-top" alt="...">
        <div class="card-body">
          <h5 class="card-title">{{pizza.name}}</h5>
          <p class="card-text">
            <ul>
                <li>
                    {{pizza.toppings.all |join:", "}}
                </li>
                <li>
                  <strong> {{pizza.price}} $</strong> 
                </li>
        </ul>
         </p>
         <div>
            <form action="" method="POST">
                {% csrf_token %}
                {{form}}
                <br>
                <input  class="btn btn-primary" type="submit" value="Order">
            </form>
         </div>
        </div>
      </div>

    </div>
{% endblock content %}

19. order sayfasında size, adet vb seçeceği bir **form** ekleyeceğim
**pizzas/forms.py** oluşturuyorum

from django import forms
from .models import Order


class PizzaForm(forms.ModelForm):
    
    class Meta:
        model = Order
**order modelinden sadece size ve order datasını alacağım, onun için forma sadece bunları koydum**
        fields = (
            'size',
            'quantity',
        )
        widgets = {
            'size':forms.RadioSelect,
            'quantity': forms.TextInput(attrs={'class' : "rounded border border-warning form-control", "style" : "width: 50%;"}),          
        }

20. Kullanıcının kendi orderlarını göreceği bir sayfa yapıyorum.

**pizzas/views.py**
(kim login ise o userın orderlarını göstersin, yani userı şu an isteği atan usera eşit olan)

def my_orders(request):
    orders = Order.objects.filter(user=request.user)
    context = {
        'orders': orders
    }
    return render(request, 'pizzas/my_orders.html', context)

**urls.py**

from .views import home, pizzas, order_view, my_orders,

urlpatterns = [
    ...,
    path('my_orders/', my_orders, name='my_orders'),
]

**navbar**
(user authenticate ise görsün istediğimden buranın altına ekledim)

  {% if user.is_authenticated %}
                    <li class="nav-item"><a class="nav-link" href="{% url 'my_orders' %}">My Orders</a></li>

**pizzas/templates/pizzas/my_orders.html**

{% extends 'pizzas/base.html' %}


{% block content %}
<div class="d-flex flex-column justify-content-center mx-auto mt-5 col-md-6">
    <div class="alert alert-warning text-center p-1"> <h1>{{user.username | upper}}'s Orders</h1></div>
    {% for order in orders  %}
    <div class="card m-2 shadow p-2" >
        <div class="row g-0 d-flex justify-content-between">
            <div class="col-md-1 d-flex align-items-center">
                <img src="{{order.pizza.image.url}}"  style=" border-radius: 50%; width: 60px; height: 60px"  alt="...">
            </div> 
            <div class="col-md-5 d-flex justify-content-start flex-fill">
                <div class="card-body">
                    <p class="card-title"><strong>Pizza:</strong> {{order.pizza}} 🍕  <strong>Size:</strong> {{order.size}} 🍕 <strong>X :</strong> {{order.quantity}} </p>              
                </div>
            </div>
            <div class="col-md-2 d-flex align-items-center rounded">
            <a href="{% url 'update_orders' order.id %}"> <button class=" btn btn-warning me-2">Update</button></a>
             <a href="{% url 'delete_orders' order.id %}"><button class=" btn btn-danger me-2">Delete</button></a>
          </div> 
        </div>
      </div>
      {% endfor %}
</div>


{% endblock content %}

21. order update

**pizzas/views.py**

def update_order_view(request, id):
    order = Order.objects.get(id=id)
    pizza = Pizza.objects.get(id=order.pizza.id)
    form = PizzaForm(instance=order) **dolu form gelecek**
    if request.method == 'POST':
        form = PizzaForm(request.POST, instance=order) **instance=order yazmasaydım yeni sipariş create ederdi, şimdi mevcut orderı update ediyor.**
        if form.is_valid():
            order.save()
            return redirect('my_orders')
    context = {
        'order': order,
        'pizza': pizza,
        "form" : form
    }
    return render(request, 'pizzas/update_order.html', context)

**pizzas.urls.py**

from .views import ..., ..., ..., ..., update_order_view

urlpatterns = [
    ...    
    ...,
    ...,
    path('update_orders/<int:id>', update_order_view, name='update_orders'),
]

**pizzas/templates/pizzas/update_order.html**

{% extends 'pizzas/base.html' %}



{% block content %}
<div class="col-lg-3 mx-auto d-flex justify-content-center align-items-center mt-5">

    <div class="card" style="width: 100%;">
        <img src="{{pizza.image.url}}" class="card-img-top" alt="...">
        <div class="card-body">
          <h5 class="card-title">{{pizza.name}}</h5>
          <p class="card-text">
            <ul>
                <li>
                    {{pizza.toppings.all |join:", "}}
                </li>
                <li>
                  <strong> {{pizza.price}} $</strong> 
                </li>
        </ul>
         </p>
         <div>
            <form action="" method="POST">
                {% csrf_token %}
                {{form}}
                <br>
                <input  class="btn btn-primary" type="submit" value="Order">
            </form>
         </div>
        </div>
      </div>

    </div>
{% endblock content %}

**pizzas/templates/pizzas/my_orders**
update'e tıkladığımda gitmesi için my_orders'a;

  <a href="{% url 'update_orders' order.id %}"> <button class=" btn btn-warning me-2">Update</button></a>
  ekliyorum

22. order delete

**views.py**

def delete_order_view(request, id):
    order = Order.objects.get(id=id)
    order.delete()
    return redirect('my_orders')

**urls.py**

from .views import ..., ..., ..., ..., ..., delete_order_view
urlpatterns = [
path('delete_orders/<int:id>', delete_order_view, name='delete_orders')
]

**pizzas/templates/pizzas/my_orders**
burada her bir cardın delete butonuna linki yerleştiriyorum;
    button></a>
    <a href="{% url 'delete_orders' order.id %}"><button class=" btn btn-danger me-2">Delete</button></a>