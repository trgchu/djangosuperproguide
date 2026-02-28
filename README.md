# djangosuperproguide

Autor: Fátima M. Herrera
TC2005B.401

---
# Creación de un ambiente virtual

```bash
python -m venv .env
```

# Activar en windows

```bash
.env\Scripts\activate
```

# Instalar Django

```bash
pip install django
```

# Instalar MySQL driver (mi caso, pero es opcional)

```bash
pip install mysqlclient
```
# Verificar instalación 

```bash
django-admin --version
```

---
# Creando un proyecto Django

```bash
django-admin startproject config
cd config
```

# Estructura generada:

```code
config/
│ manage.py
│
└── config/
    │ settings.py
    │ urls.py
    │ wsgi.py
```

# Habilitar base de datos MySQL (settings.py)

```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'django_db',
        'USER': 'django_user',
        'PASSWORD': 'tu_password',
        'HOST': 'localhost',
        'PORT': '3306',
    }
}
```

# Aplicar migraciones

```bash
python manage.py migrate
```

---
# Creando una app en Django

```bash
python manage.py startapp blog
```

# Agregar en settings.py:

```python
INSTALLED_APPS = [
    ...
    'blog',
]
```

---
# Estructura de archivos

```code
project/
│ manage.py
│
├── config/
│   ├── settings.py
│   ├── urls.py
│
└── blog/
    ├── models.py
    ├── views.py
    ├── urls.py
    ├── templates/
    ├── forms.py
```

# Archivos importantes:

```code
models.py --> Define estructura de datos
views.py --> Lógica del servidor
urls.py --> Rutas
templates/ --> HTML
settings.py --> Configuración global
```

---
# Probando modificaciones

```bash
python manage.py makemigrations
python manage.py migrate
```

# Correr servidor

```bash
python manage.py runserver
```

# Vamos a:

```code
http://127.0.0.1:8000/
```

---
# Habilitar URL para mostrar todas las entidades

# Modelo

```python
from django.db import models

class Article(models.Model):
    name = models.CharField(max_length=200)
    content = models.TextField()
```

# Vista (views.py)

```python
from django.views.generic import ListView
from .models import Article

class ListArticlesView(ListView):
    model = Article
    context_object_name = 'articles_list'
```

# URL (blog/urls.py)

```python
from django.urls import path
from . import views

urlpatterns = [
    path('', views.ListArticlesView.as_view(), name='article_list'),
]
```

# Conectar la app en el proyecto principal (config/urls.py)

```python
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('blog.urls')),
]
```

# Template (templates/list.html)

```html
{% for article in articles_list %}
    <h2>{{ article.name }}</h2>
    <p>{{ article.content }}</p>
{% endfor %}
```

---
# Habilitar URL para crear nueva entidad

# Formulario (forms.py)

```python
from django.forms import ModelForm
from .models import Article

class ArticleForm(ModelForm):
    class Meta:
        model = Article
        fields = "__all__"
```

# Vista (views.py)

```python
from django.shortcuts import render, redirect
from django.views import View
from .forms import ArticleForm

class NewArticleView(View):
    def get(self, request):
        form = ArticleForm()
        return render(request, 'new.html', {'form': form})

    def post(self, request):
        form = ArticleForm(request.POST)
        if form.is_valid():
            form.save()
            return redirect('article_list')
```

# URL (config/urls.py)

```python
path('new/', views.NewArticleView.as_view(), name='new_article'),
```

# Template (templates/new.html)

```html
<form method="POST">
    {% csrf_token %}
    {{ form.as_p }}
    <button type="submit">Guardar</button>
</form>
```
