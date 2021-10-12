*Two Scoops of Django 1.11: Best Practices for Django Fourth Edition*
=====================================================================

## Introduction

- [django](https://www.djangoproject.com/)
- [twoscoops's github](https://github.com/twoscoops)
- [12factor](https://12factor.net/)

## 1 Coding Style

- [django coding style](https://docs.djangoproject.com/en/1.11/internals/contributing/writing-code/coding-style/)

### 1.1 The Importance of Making Your Code Readable

关心代码的可读性

### 1.2 PEP 8

- [Flake8](https://flake8.pycqa.org/en/latest/)

### 1.3 The Word on Imports

- future
- standard library
- third-party
- Django
- local Django

+ import
+ from import

### 1.4 Use Explicit Relative Imports

### 1.5 Avoid Using Import *

### 1.6 Django Coding Style

### 1.7 Choose JS, HTML, and CSS Style Guides

- [eslint](https://eslint.org/)

### 1.8 Never Code to the IDE (Or Text Editor)

### 1.9 Summary

**遵循一致的编码风格**

## 2 The Optimal Django Environment Setup

### 2.1 Use the Same Database Engine Everywhere

各个环境使用一致的数据库配置，并且不要依赖fixtures

### 2.2 Use Pip and Virtualenv

### 2.3 Install Django and Other Dependencies via Pip

### 2.4 Use a Version Control System

### 2.5 Optional: Identical Environments

- 用Docker创建相同的环境
    - [cookiecutter-django](https://cookiecutter-django.readthedocs.io/en/latest/developing-locally-docker.html)
    - [Real Python article on Django and Docker Compose](http://bit.ly/1dWnzVW)
    - [dockerbook](https://dockerbook.com)

### 2.6 Summary

保持一致的环境设置

## 3 How to Lay Out Django Projects

- [cookiecutter-django](https://github.com/pydanny/cookiecutter-django)
- [cookiecutters](https://djangopackages.org/grids/g/cookiecutters/)

### 3.1 Django 1.11’s Default Project Layout

### 3.2 Our Preferred Project Layout

```
<repository_root>/
├── <configuration_root>/
├── <django_project_root>/
```

### 3.3 Sample Project Layout

```
icecreamratings_project
├── config/
│   ├── settings/
│   ├── __init__.py
│   ├── urls.py
│   └── wsgi.py
├── docs/
├── icecreamratings/
│   ├── media/ # Development only!
│   ├── products/
│   ├── profiles/
│   ├── ratings/
│   ├── static/
│   └── templates/
├── .gitignore
├── Makefile
├── README.rst
├── manage.py
└── requirements.txt
```

### 3.4 What About the Virtualenv?

不需要在版本控制添加.venv只要执行下列命令

```
$ pip freeze > requirements.txt
```

### 3.5 Going Beyond startproject

- [cookiecutter-django](https://github.com/pydanny/cookiecutter-django)

### 3.6 Summary

推荐使用cookiecutter

## 4 Fundamentals of Django App Design

### 4.1 The Golden Rule of Django App Design

**each app should be tightly focused on its task**

### 4.2 What to Name Your Django Apps

应用程序名称推荐单个单词的复数形式，一般为主Model的复数形式，同时需要考虑到URL规则

### 4.3 When in Doubt, Keep Apps Small

### 4.4 What Modules Belong in an App?

```
# Common modules
scoops/
├── __init__.py
├── admin.py
├── forms.py
├── management/
├── migrations/
├── models.py
├── templatetags/
├── tests/
├── urls.py
├── views.py

# uncommon modules
scoops/
├── api/
├── behaviors.py
├── constants.py
├── context_processors.py
├── decorators.py
├── db/
├── exceptions.py
├── fields.py
├── factories.py
├── helpers.py
├── managers.py
├── middleware.py
├── signals.py
├── utils.py
├── viewmixins.py
```

### 4.5 Summary

**It’s an art, not a science.**

## 5 Settings and Requirements Files

1. All settings files need to be version-controlled
2. Don’t Repeat Yourself
3. Keep secret keys safe

### 5.1 Avoid Non-Versioned Local Settings

### 5.2 Using Multiple Settings Files

### 5.3 Separate Configuration From Code

- 使用环境变量
- settings中不要导入ImproperlyConfigured以外的Django Components
- [多个settings时使用django-admin代替manage.py](https://docs.djangoproject.com/en/1.11/ref/django-admin/)

### 5.4 When You Can’t Use Environment Variables

- 不能使用环境变量时使用文件配置(JSON/.env/...)并加入到.gitignore

### 5.5 Using Multiple Requirements Files

为requirements.txt设置明确的版本号

```
requirements/
├── base.txt
├── local.txt
├── staging.txt
├── production.txt

# requirements/base.txt
Django==1.11.0
psycopg2==2.6.2
djangorestframework==3.4.0

# requirements/local.txt
-r base.txt # includes the base.txt requirements file

coverage==4.2
django-debug-toolbar==1.5
```

### 5.6 Handling File Paths in Settings

**Don’t hardcode your paths!, pathlib or os**

### 5.7 Summary

分版本管理配置文件，使用环境变量来配置Secrets，其余内容都添加到版本控制

## 6 Model Best Practices

- django-model-utils
- django-extensions

### 6.1 Basics

- 控制每个app包含5个左右的models，否则尝试分解apps

- [model-inheritance](https://docs.djangoproject.com/en/1.11/topics/db/models/#model-inheritance)
- 只有在重复字段多时才使用抽象基类
- proxy models偶尔有用
- 避免multi-table inheritance(为父类model也创建了表)

```
from django.db import models


class TimeStampedModel(models.Model):
    """
    An abstract base class model that provides selfupdating ``gmt_create`` and ``gmt_modified`` fields.
    """
    gmt_create = models.DateTimeField(auto_now_add=True)
    gmt_modified = models.DateTimeField(auto_now=True)

    class Meta:
        abstract = True
```

### 6.2 Database Migrations

- **always backup before migrations**
- [RunPython before RunSQL](https://docs.djangoproject.com/en/3.2/ref/migration-operations/)

### 6.3 Overcoming Common Obstacles of RunPython

- 读取Custom Model Manager
    - `use_in_migrations = True` to our custom
managers
- 读取Custom Model Method
    - 迁移时无法调用任何method
- Use RunPython.noop to Do Nothing
- 迁移
    - 备份
    - 测试
    - 回滚

### 6.4 Django Model Design

- Cache Before Denormalizing
- null
    - 数据库NULL
- blank
    - form widget input = ""
- 设置blank的时候需要同时设置null为True
- 二进制数据
    - BinaryField
    - 更好的选择是使用文件
    - database speed < file speed
- 避免使用Generic Relations
- Choices(可以使用Enum)

### 6.5 The Model _meta API

- [_meta](https://docs.djangoproject.com/en/1.11/ref/models/meta/)
- 和Model的字段相关的可以考虑使用_meta

### 6.6 Model Managers

- Model inheritance中只有抽象父类的Manager会被继承,具体父类的不会被继承
- 父类的Model Managers会被作为Model的第一个Manager
- 把objects = models.Manager()设置在自定义Manager之上

### 6.7 Understanding Fat Models

- 通过Mixins和辅助函数将数据相关方法封装到Models

## 7 Queries and the Database Layer

### 7.1 Use get_object_or_404() for Single Objects

- 只在View相关中使用

### 7.2 Be Careful With Queries That Might Throw Exceptions

- ObjectDoesNotExist可用于所有Models
- DoesNotExist需要前缀具体Model,SpecificModel.DoesNotExist
- SpecificModel.MultipleObjectsReturned

### 7.3 Use Lazy Evaluation to Make Queries Legible

```
# Django ORM只有在最后取值时才会进行SQL调用,可以将查询分解为多行
def fun_function(name=None):
    """Find working ice cream promo"""
    results = Promo.objects.active()
    results = results.filter(
        Q(name__startswith=name) | Q(description__icontains=name)
            )
    results = results.exclude(status='melted')
    results = results.select_related('flavors')
    return results
```

### 7.4 Lean on Advanced Query Tools

- 尽量使用ORM相关数据工具并且避免通过Python遍历QuerySet来处理数据

### 7.5 Don’t Drop Down to Raw SQL Until It’s Necessary

### 7.6 Add Indexes as Needed

- 索引
    - 索引被频繁使用
    - 索引确实起到效果
- 添加方式
    - db_index=True
    - Meta.indexes

### 7.7 Transactions

- 对数据库有写操作的时候使用事务
- 对django.http.StreamingHttpResponse使用事务无效
- [transactions](https://docs.djangoproject.com/en/1.11/topics/db/transactions/)

### 7.8 Summary

多使用ORM

## 8 Function- And Class-Based Views

### 8.1 When to Use FBVs or CBVs

### 8.2 Keep View Logic Out of URLConfs

### 8.3 Stick to Loose Coupling in URLConfs

### 8.4 Use URL Namespaces

```
urlpatterns += [
    url(r'^tastings/', include('tastings.urls', namespace='tastings')),
]
```

### 8.5 Try to Keep Business Logic Out of Views

### 8.6 Django Views Are Functions

```
HttpResponse = view(HttpRequest)

HttpResponse = View.as_view()(HttpRequest)
```

### 8.7 Don’t Use locals() as Views Context

### 8.8 Summary

## 9 BestPractices for Function-Based Views

### 9.1 Advantages of FBVs

### 9.2 Passing the HttpRequest Object

### 9.3 Decorators Are Sweet

### 9.4 Passing the HttpResponse Object

### 9.5 Summary

考虑使用装饰器来抽取FBV中重复的逻辑

## 10 BestPractices for Class-Based Views

- [django-braces](https://github.com/brack3t/django-braces)

### 10.1 Guidelines When Working With CBVs

- [Classy Class-Based Views](http://ccbv.co.uk/)

### 10.2 Using Mixins With CBVs

### 10.3 Which Django GCBV Should Be Used for What Task?

### 10.4 General Tips for Django CBVs

### 10.5 How GCBVs and Forms Fit Together

### 10.6 Using Just django.views.generic.View

### 10.7 Additional Resources

- [CBV](https://docs.djangoproject.com/en/1.11/topics/class-based-views/)
- django-extra-views
- django-vanilla-views

### 10.8 Summary

## 11 Common Patterns for Forms

### 11.1 Pattern 1: Simple ModelForm With Default Validators

### 11.2 Pattern 2: Custom Form Field Validators in ModelForms

### 11.3 Pattern 3: Overriding the Clean Stage of Validation

### 11.4 Pattern 4: Hacking Form Fields (2 CBVs, 2 Forms, 1 Model)

### 11.5 Pattern 5: Reusable Search Mixin View

### 11.6 Summary

对于Form fields验证的一些最佳实践

## 12 Form Fundamentals

### 12.1 Validate All Incoming Data With Django Forms

### 12.2 Use the POST Method in HTML Forms

### 12.3 Always Use CSRF Protection With HTTP Forms That Modify Data

- [CSRF](https://tech.meituan.com/2018/10/11/fe-security-csrf.html)

### 12.4 Understand How to Add Django Form Instance Attributes

### 12.5 Know How Form Validation Works

- ModelForm Data Is Saved to the Form, Then the Model Instance

### 12.6 Add Errors to Forms With Form.add_error()

### 12.7 Fields Without Pre-Made Widgets

### 12.8 Customizing Widgets

### 12.9 Additional Resources

### 12.10 Summary

- TODO
    - 自己debug一下django form的整个数据链路
