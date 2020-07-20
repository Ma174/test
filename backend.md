# Тест на вакансию Backend-разработчик Mind&Machine

В рамках данного теста необходимо ответить на вопросы связанные с работой Django, celery и других типовых инструментов.

Выполнил: 

Контакт: 


## Блок №1: гуру Django

Предположим, что разрабатываем общедоступный сайт для магазина одежды (например сайт для adidas). Для этого в системе должны быть модели:

- Item -- товар магазина (из одежды), соотвественно есть цена, названия, для какого пола.
- User -- пользователь, клиент с возможностью захода в личный кабинет:


```python

import datetime

from django.db import models
from django.contrib.auth.models import AbstractUser as DjangoAbstractUser


class Item(models.Model):
    dttm_created = models.DateTimeField(default=datetime.datetime.now)
    dttm_deleted = models.DateTimeField()

    price = models.FloatField()


class User(DjangoAbstractUser):
    dttm_created = models.DateTimeField(default=datetime.datetime.now)
    dttm_deleted = models.DateTimeField()

    SEX_FEMALE = 'F'
    SEX_MALE = 'M'
    SEX_CHOICES = (
        (SEX_FEMALE, 'Female',),
        (SEX_MALE, 'Male',),
    )

    sex = models.CharField(max_length=1,  choices=SEX_CHOICES)
    bought_items = models.ManyToManyField(Item)

```


Из ТЗ виртуального заказчика необходимо, чтобы в системе было видно, что и когда клиент магазина купил.

-----

##### Вопрос №1: Соответствует ли база требованиям? Как стоит реализовать?


_(место для ответа)_


-----

##### Вопрос №2: есть ли в модели User лишние поля?

_(место для ответа)_


-----


##### Вопрос №3: По ТЗ необходимо написать запрос, который возвращает количество вещей и их стоимость для каждого клиента, которые они купили. список клиентов (id) передается в запросе:

```python

import datetime
(место для кода)


def view(request):
    user_ids = request.GET.getlist('ids')
    return_data = User.objects.  (место для кода)

    ...

    return response

```

-----

##### Вопрос №4: По ТЗ необходимо написать запрос, который возвращает список товаров магазинов, которые удовлевторяют следующим фильтрам: фильтр по полу клиентов, которые покупали товар; фильтр по цене. Если указаны оба фильтра, то выводятся только те товары, которые удовлетворяют обоим фильтрам. При этом, вместе с атрибутами модели Item надо еще вернуть  количество клиентов, которые купили этот товар и зарегистрированы  в системе до 2019.05.01 (если еще пол указан в фильтре, то соотвественно у считаемых клиентов должен быть этот пол):

```python

import datetime
(место для кода)

from django import forms


class ViewForm(forms.Form):
    price = forms.FloatField(required=False)
    sex = forms.CharField(max_length=1, required=False)



def view(request):
    edge_date = datetime.date(2019, 5, 1)

    form = ViewForm(request.GET)
    if form:
        return_data = Item.objects. (место для кода)


    ...

    return response

```

-----

##### Вопрос №5: В бд необходимо добавить новую модель Seller (продавец магазина). Причем каждый клиент должен быть привязан к Seller (клиент сформировал ТЗ  на обновление после 3 месяцев функционирования сайта (есть данные в бд)):

```python

class User(DjangoAbstractUser):
    dttm_created = models.DateTimeField(default=datetime.datetime.now)
    dttm_deleted = models.DateTimeField()

    SEX_FEMALE = 'F'
    SEX_MALE = 'M'
    SEX_CHOICES = (
        (SEX_FEMALE, 'Female',),
        (SEX_MALE, 'Male',),
    )

    sex = models.CharField(max_length=1,  choices=SEX_CHOICES)
    bought_items = models.ManyToManyField(Item)

    seller = models.ForeignKey('Seller', on_delete=models.PROTECT)
    
class Seller(models.Model):
    name = models.CharField(max_length=100)

```

##### Что необходимо сделать и как, чтобы реализовать обновление?

(место для кода/ответа)


-----


## Блок №2: мастер на все руки

##### Вопрос №6: Как развернуть django на боевом сервере? что нужно/можно использовать?

_(место для ответа)_


##### Вопрос №7: Redis или RabbitMQ для Celery? Почему?

_(место для ответа)_




##### Вопрос №8: Какие подходы к реализации иерархии в бд есть?

_(место для ответа, по каждому подходу 1,2 предложения)_


##### Вопрос №9: Реализуем проект для клиента, по ТЗ необходимо, чтобы система выдерживала 1 млн запросов в минуту (не обязательно на чтение). Какую свободную реляционную СУБД стоит использовать?

_(место для ответа)_

