# Overview - FAQ

- O que é Django?
Django é um framwork da linguagem python.

- Quando foi criado?

- Qual a versão mais recente?

- Quantos projetos atualmente utilizam o Django?

- Como ele é comparado com outros frameworks python?

# Instalação

A versão mais recente pode ser usada junto com qualquer versão do python 3.x.y

```bash
sudo apt install python3
sudo apt install python3-pip -y

pip3 install virtualenv
```

## Virtualenv

![Virtualenv](https://miro.medium.com/max/1400/1*wC-mrXuYgarKP8bSK3AivQ.png)

Podemos instalar ele na versão padrão do sistema ou podemos utilizar um virtualenv. A vantagem é que podemos ter várias versões do python com várias do Django com diferentes versões de pacotes.

```bash
virtualenv -p /usr/bin/python3 venv
source venv/bin/activate
pip3 install Django
```

Verificando o django

```python
>>> import django
>>> print(django.get_version())
2.2
```

# Criando um projeto
Um projeto é uma coleção de aplicações e configurações para determinado website. O Django fornece uma CLI para criarmos um projeto com maior facilidade.

```bash
django-admin startproject mysite
```

Devemos ter a seguinte estrutura de diretórios

```bash
mysite/
    manage.py
    mysite/
        __init__.py
        settings.py
        urls.py
        wsgi.py
```

Com isso já podemos iniciar um servidor web com o Django

```bash
cd mysite
python manage.py runserver
```

# Criando uma aplicação
A aplicação no Djando é um pacote python com alguma funcionalidade que segue uma estrutura pré definida.

```bash
mkdir mysite/mysite/polls
django-admin startapp mysite/mysite/polls
```

Devemos ter a seguinte estrutura de diretórios:

```bash
├── manage.py
└── mysite
    ├── __init__.py
    ├── pools
    │   ├── admin.py
    │   ├── apps.py
    │   ├── __init__.py
    │   ├── migrations
    │   │   └── __init__.py
    │   ├── models.py
    │   ├── tests.py
    │   └── views.py
    ├── settings.py
    ├── urls.py
    └── wsgi.py
```

# Django MVT
![Django MVT imagem](https://www.tutorialspoint.com/django/images/django_mvc_mvt_pattern.jpg)

Uma tradução grosseira seria:

M = Model

V = View = Controller

T = Template = View

# Criando a View

Vamos criar uma view do tipo mais simples possível:

[polls/views.py](polls/views.py)

```python
from django.http import HttpResponse


def index(request):
    return HttpResponse("Hello, world. You're at the polls index.")
```

Para organizar nossas urls, crie um arquivo polls/urls.py

[polls/urls.py](polls/urls.py)

```python
from django.urls import path

from . import views

urlpatterns = [
    path('', views.index, name='index'),
]
```

Agora vamos chamar nossas urls do polls no urls da raiz:

[mysite/urls.py](mysite/mysite/urls.py)

```python
from django.contrib import admin
from django.urls import include, path

urlpatterns = [
    path('polls/', include('polls.urls')),
    path('admin/', admin.site.urls),
]
```

Rodamos o comando para subir um servidor e acessamos a URL:

```bash
python manage.py runserver
```
# Criando a Model

O Django possui um poderoso ORM que podemos utilizar através das models:

[polls/models.py](polls/models.py)

```python
from django.db import models


class Question(models.Model):
    question_text = models.CharField(max_length=200)
    pub_date = models.DateTimeField('date published')


class Choice(models.Model):
    question = models.ForeignKey(Question, on_delete=models.CASCADE)
    choice_text = models.CharField(max_length=200)
    votes = models.IntegerField(default=0)
```

Precisamos agora ativar as Models do app configurando ele dentro do settings do projeto:

[myapp/settings.py](myapp/settings.py)
```python
INSTALLED_APPS = [
    'polls.apps.PollsConfig',
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
]
```

O Django consegue detectar mudanças nas models e, automaticamente, gerar as migrations:
```bash
python manage.py makemigrations
```

Depois precisamos aplicar as migrações que foram geradas:
```bash
python manage.py migrate
```

# Django Admin
Primeira coisa que precisamos fazer é criar um usuário para acessar o painel:
```bash
python manage.py createsuperuser
```
Vamos rodar o server novamente:
```bash
python manage.py runserver
```
Depois, podemos acessar a página de admin [localhost:8000/admin/](http://localhost:8000/admin/) e fazer o login:

Inicialmente, temos as informações de usuários e grupos padrão do Django, mas podemos adicionar nossas próprias models:
[polls/admin.py](polls/admin.py)
```python
from django.contrib import admin

from .models import Question

admin.site.register(Question)
```
