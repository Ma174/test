# Тест на вакансию Backend-разработчик Mind&Machine

В рамках данного теста необходимо ответить на вопросы связанные с работой Django, celery и других типовых инструментов.

Выполнил: 

Контакт: +79998892981 Whatsapp/Telegram


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

-----models.py
from django.db import models
from django.utils import timezone
from django.contrib.auth.models import AbstractBaseUser
from django.contrib.auth.models import PermissionsMixin
    SEX_FEMALE = 'F'
    SEX_MALE = 'M'
    SEX_CHOICES = (
        (SEX_FEMALE, 'Female',),
        (SEX_MALE, 'Male',),
    )
class Userr(AbstractBaseUser, PermissionsMixin):
    email = models.EmailField(_('email address'), unique=True)
    first_name = models.CharField(_('first name'), max_length=30, blank=True)
    last_name = models.CharField(_('last name'), max_length=30, blank=True)
    date_joined = models.DateTimeField(_('date joined'), auto_now_add=True)
    is_active = models.BooleanField(_('active'), default=True)
    sex = models.CharField(max_length=1,  choices=SEX_CHOICES)
    class Meta:
    verbose_name = 'User'
    
    objects = UserManager()

    USERNAME_FIELD = 'email'
    REQUIRED_FIELDS = []

Ещё бы создал отдельную папку по типу корзины и там отслеживал , что и когда было куплено клиентом:

----cart/models.py
from catalog.models import Product
class Cart():
    def add(self, product, quantity=1):
        product_id = int(product.id)
        if product_id not in self.cart:
            self.cart[product_id] = {'quantity': 0}
        if     
            self.cart[product_id]['quantity'] += quantity
     def drop(self, product):
        product_id = str(product.id)
        if product_id in self.cart:
            del self.cart[product_id]
            self.save()
     def get_items(self):
        return self.cart
-----cart/models.py
from django.db import models
from .models import Product
class Order(models.Model):
    user = models.ForeignKey(settings.AUTH_USER_MODEL)
    created = models.DateTimeField(auto_now_add=True)
    Class Meta:
        ordering = ('-created',)
        verbose_name = 'Order'
class OrderItem(modeldModel):
    order = models.ForeignKey(Order)
    product = models.ForeignKey(Product)
    quantity = models.PositiveIntegerField(default=1)
Единственное, я пока что не до конца придумал, как учитывать увеличение кол-ва товара.
##### Вопрос №2: есть ли в модели User лишние поля?

Не уверен, что нужно отслеживать дату удаления юзера, я бы просто вместо этого сделал бы поле is_active = models.BooleanField(_('active'), default=True)

-----


##### Вопрос №3: По ТЗ необходимо написать запрос, который возвращает количество вещей и их стоимость для каждого клиента, которые они купили. список клиентов (id) передается в запросе:

```python

import datetime
from django.db.models import Count


def view(request):
    user_ids = request.GET.getlist('ids')
    return_data = User.objects.filter(pk__in=user_ids).prefetch_related('bought').aggregate(
                    i_count=Count('bought')

    ...

    return response

```

-----

##### Вопрос №4: По ТЗ необходимо написать запрос, который возвращает список товаров магазинов, которые удовлевторяют следующим фильтрам: фильтр по полу клиентов, которые покупали товар; фильтр по цене. Если указаны оба фильтра, то выводятся только те товары, которые удовлетворяют обоим фильтрам. При этом, вместе с атрибутами модели Item надо еще вернуть  количество клиентов, которые купили этот товар и зарегистрированы  в системе до 2019.05.01 (если еще пол указан в фильтре, то соотвественно у считаемых клиентов должен быть этот пол):

```python

import datetime
from django import forms


class ViewForm(forms.Form):
    price = forms.FloatField(required=False)
    sex = forms.CharField(max_length=1, required=False)



def view(request):
    edge_date = datetime.date(2019, 5, 1)

    form = ViewForm(request.GET)
    if form:
        return_data=Item.objects.all().filter(user__sex=form.cleaned_data['sex'], 
                                                  price=form.cleaned_data['price'],
                                                  user__dttm_created>edge_date)
        return_data.count=return_data.count()



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

присвоить стандартное значение всем:
seller = models.ForeignKey('Seller',default='Update', on_delete=models.PROTECT)


-----


## Блок №2: мастер на все руки

##### Вопрос №6: Как развернуть django на боевом сервере? что нужно/можно использовать?

Nginx+Gunicorn+Django


##### Вопрос №7: Redis или RabbitMQ для Celery? Почему?

RabbitMQ за счет своей многопоточности, ведь неизвестно, сколько будет работников.




##### Вопрос №8: Какие подходы к реализации иерархии в бд есть?

Один ко многим, когда от общего поля бд идет много связей ко многим полям бд. 
Много ко многим, когда к каждому полю бд может быть сколько угодно связей с другими полями бд.
_(место для ответа, по каждому подходу 1,2 предложения)_


##### Вопрос №9: Реализуем проект для клиента, по ТЗ необходимо, чтобы система выдерживала 1 млн запросов в минуту (не обязательно на чтение). Какую свободную реляционную СУБД стоит использовать?

Postgresql

