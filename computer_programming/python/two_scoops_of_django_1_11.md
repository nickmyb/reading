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

## 13 Templates: Best Practices

### 13.1 Keep Templates Mostly in templates/

### 13.2 Template Architecture Patterns

```
templates/
├── base.html
├── dashboard.html # extends base.html
├── profiles/
├────── profile_detail.html # extends base.html
├────── profile_form.html # extends base.html

templates/
├── base.html
├── dashboard.html # extends base.html
├── profiles/
├────── base_profiles.html # extends base.html
├────── profile_detail.html # extends base_profiles.html
├────── profile_form.html # extends base_profiles.html
```

### 13.3 Limit Processing in Templates

- templates只传入被处理后需要展示的部分数据
- 涉及外键时使用select_related,否则会有额外的数据库查询
- 尽量不要在template中调用对象的方法来处理image和API这些隐含的调用

### 13.4 Don’t Bother Making Your Generated HTML Pretty

### 13.5 Exploring Template Inheritance

### 13.6 block.super Gives the Power of Control

### 13.7 Useful Things to Consider

### 13.8 Error Page Templates

- [github styleguide](https://styleguide.github.com/)

### 13.9 Follow a Minimalist Approach

### 13.10 Summary

## 14 Template Tags and Filters

### 14.1 Filters Are Functions

### 14.2 Custom Template Tags

### 14.3 Naming Your Template Tag Libraries

### 14.4 Loading Your Template Tag Modules

### 14.5 Summary

## 15 Django Templates and Jinja2

### 15.1 What’s the Syntactical Difference?

### 15.2 Should I Switch?

### 15.3 Considerations When Using Jinja2 With Django

### 15.4 Resources

### 15.5 Summary

## 16 Building REST APIs With Django REST Framework

### 16.1 Fundamentals of Basic REST API Design

- [status code](https://en.wikipedia.org/wiki/List_of_HTTP_status_codes)

### 16.2 Illustrating Design Concepts With a Simple API

- [Classy Django REST Framework](https://www.cdrf.co/)

### 16.3 REST API Architecture

```
直接创建apivX的app

flavors/
├── api/
│    ├── __init__.py
│    ├── authentication.py
│    ├── parsers.py
│    ├── permissions.py
│    ├── renderers.py
│    ├── serializers.py
│    ├── validators.py
│    ├── views.py
│    ├── viewsets.py

flavors/
├── api/
│    ├── __init__.py
│    ├── ...
│    ├── views.py
│    │    ├── flavor.py
│    │    ├── ingredient.py
│    ├── viewsets.py
```

- 通过添加core/apivX_urls.py来绑定多个APIView
- 文档化权限验证方案

### 16.4 When DRF Gets in the Way

- 逻辑复杂时使用基础的APIView
- 将API进行拆分

### 16.5 Shutting Down an External API

### 16.6 Rate-Limiting Your API

### 16.7 Advertising Your REST API

### 16.8 Additional Reading

### 16.9 Summary

## 17 Consuming REST APIs

### 17.1 Learn How to Debug the Client

### 17.2 Consider Using JavaScript-Powered Static Asset Preprocessors

### 17.3 Real-Time Woes a.k.a. Latency

### 17.4 Avoid the Anti-Patterns

- [test 4 javascript](https://stackoverflow.com/questions/300855/javascript-unit-test-tools-for-tdd)

### 17.5 AJAX and the CSRF Token

### 17.6 Improving JavaScript Skills

### 17.7 Follow JavaScript Coding Standards

### 17.8 Summary

## 18 Tradeoffs of Replacing Core Components

### 18.1 The Temptation to Build FrankenDjango

### 18.2 Non-Relational Databases vs. Relational Databases

### 18.3 What About Replacing the Django Template Language?

### 18.4 Summary

尽可能的使用Django的基础组件，有足够可信的理由支撑再尝试替换

## 19 Working With the Django Admin

### 19.1 It’s Not for End Users

### 19.2 Admin Customization vs. New Views

### 19.3 Viewing String Representations of Objects

### 19.4 Adding Callables to ModelAdmin Classes

### 19.5 Be Aware of the Complications of Multiuser Environments

### 19.6 Django’s Admin Documentation Generator

### 19.7 Using Custom Skins With the Django Admin

### 19.8 Secure the Django Admin

### 19.9 Securing the Admin Docs

### 19.10 Summary

## 20 Dealing With the User Model

### 20.1 Use Django’s Tools for Finding the User Model

### 20.2 Custom User Fields for Django 1.11 Projects

- [Custom User django-authtools](https://github.com/fusionbox/django-authtools)

### 20.3 Summary

## 21 Django’s Secret Sauce: Third-Party Packages

### 21.1 Examples of Third-Party Packages

### 21.2 Know About the Python Package Index

### 21.3 Know About DjangoPackages.org

### 21.4 Know Your Resources

### 21.5 Tools for Installing and Managing Packages

### 21.6 Package Requirements

### 21.7 Wiring Up Django Packages: The Basics

### 21.8 Troubleshooting Third-Party Packages

### 21.9 Releasing Your Own Django Packages

### 21.10 What Makes a Good Django Package?

### 21.11 Creating Your Own Packages the Easy Way

```
# Only if you haven't installed cookiecutter yet
$ pip install cookiecutter

# Creating a Django Package from scratch
$ cookiecutter https://github.com/pydanny/cookiecutter-djangopackage.git

# Creating a Python Package from scratch
$ cookiecutter https://github.com/audreyr/cookiecutter-pypackage.git
```

### 21.12 Maintaining Your Open Source Package

### 21.13 Additional Reading

### 21.14 Summary

## 22 Testing Stinks and Is a Waste of Money!

### 22.1 Testing Saves Money, Jobs, and Lives

- [coverage.py](https://coverage.readthedocs.io/en/6.0.2/)

### 22.2 How to Structure Tests

```
flavors/
├── tests/
│    ├── __init__.py
│    ├── test_forms.py
│    ├── test_models.py
│    ├── test_views.py
```

### 22.3 How to Write Unit Tests

### 22.4 What About Integration Tests?

### 22.5 Continuous Integration

### 22.6 Who Cares? We Don’t Have Time for Tests!

### 22.7 The Game of Test Coverage

### 22.8 Setting Up the Test Coverage Game

### 22.9 Playing the Game of Test Coverage

### 22.10 Alternatives to unittest

### 22.11 Summary

## 23 Documentation: Be Obsessed

### 23.1 Use reStructuredText for Python Docs

### 23.2 Use Sphinx to Generate Documentation From reStructuredText

### 23.3 What Docs Should Django Projects Contain?

### 23.4 Additional Documentation Resources

### 23.5 The Markdown Alternative

### 23.6 Wikis and Other Documentation Methods

### 23.7 Summary

## 24 Finding and Reducing Bottlenecks

### 24.1 Should You Even Care?

### 24.2 Speed Up Query-Heavy Pages

- [django-debug-toolbar](https://github.com/jazzband/django-debug-toolbar)
- [django-cache-panel](https://github.com/lincolnloop/django-cache-panel)
- [django-extensions](https://github.com/django-extensions/django-extensions)
- [silk](https://github.com/jazzband/django-silk)

### 24.3 Get the Most Out of Your Database

### 24.4 Cache Queries With Memcached or Redis

### 24.5 Identify Specific Places to Cache

### 24.6 Consider Third-Party Caching Packages

### 24.7 Compression and Minification of HTML, CSS, and JavaScript

### 24.8 Use Upstream Caching or a Content Delivery Network

### 24.9 Other Resources

### 24.10 Summary

## 25 Asynchronous Task Queues

### 25.1 Do We Need a Task Queue?

### 25.2 Choosing Task Queue Software

### 25.3 Best Practices for Task Queues

### 25.4 Resources for Task Queues

### 25.5 Summary

## 26 Security Best Practices

### 26.1 Reference Security Sections in Other Chapters

### 26.2 Harden Your Servers

### 26.3 Know Django’s Security Features

### 26.4 Turn Off DEBUG Mode in Production

### 26.5 Keep Your Secret Keys Secret

### 26.6 HTTPS Everywhere

- [免费HTTPS证书](https://letsencrypt.org/)

### 26.7 Use Allowed Hosts Validation

### 26.8 Always Use CSRF Protection With HTTP Forms That Modify Data

### 26.9 Prevent Against Cross-Site Scripting (XSS) Attacks

### 26.10 Defend Against Python Code Injection Attacks

### 26.11 Validate All Incoming Data With Django Forms

### 26.12 Disable the Autocomplete on Payment Fields

### 26.13 Handle User-Uploaded Files Carefully

### 26.14 Don’t Use ModelForms.Meta.exclude

### 26.15 Don’t Use ModelForms.Meta.fields = "__all__"

### 26.16 Beware of SQL Injection Attacks

### 26.17 Never Store Credit Card Data

### 26.18 Monitor Your Sites

### 26.19 Keep Your Dependencies Up-to-Date

### 26.20 Prevent Clickjacking

### 26.21 Guard Against XML Bombing With defusedxml

### 26.22 Explore Two-Factor Authentication

### 26.23 Embrace SecurityMiddleware

### 26.24 Force the Use of Strong Passwords

### 26.25 Give Your Site a Security Checkup

### 26.26 Put Up a Vulnerability Reporting Page

### 26.27 Never Display Sequential Primary Keys

### 26.28 Reference Our Security Settings Appendix

### 26.29 Review the List of Security Packages

### 26.30 Keep Up-to-Date on General Security Practices

### 26.31 Summary

## 27 Logging: What’s It For, Anyway?

### 27.1 Application Logs vs. Other Logs

### 27.2 Why Bother With Logging?

### 27.3 When to Use Each Log Level

### 27.4 Log Tracebacks When Catching Exceptions

### 27.5 One Logger Per Module That Uses Logging

### 27.6 Log Locally to Rotating Files

- logrotate
- logging.handlers.WatchedFileHandler

### 27.7 Other Logging Tips

### 27.8 Necessary Reading Material

### 27.9 Useful Third-Party Tools

### 27.10 Summary

## 28 Signals: Use Cases and Avoidance Techniques

### 28.1 When to Use and Avoid Signals

### 28.2 Signal Avoidance Techniques

### 28.3 Summary

## 29 What About Those Random Utilities?

### 29.1 Create a Core App for Your Utilities

### 29.2 Optimize Apps With Utility Modules

### 29.3 Django’s Own Swiss Army Knife

### 29.4 Exceptions

### 29.5 Serializers and Deserializers

### 29.6 Summary

## 30 Deployment: Platforms as a Service

### 30.1 Evaluating a PaaS

### 30.2 Best Practices for Deploying to PaaS

### 30.3 Summary

## 31 Deploying Django Projects

### 31.1 Single-Server for Small Projects

### 31.2 Multi-Server for Medium to Large Projects

### 31.3 WSGI Application Servers

### 31.4 Performance and Tuning: uWSGI and Gunicorn

### 31.5 Stability and Ease of Setup: Gunicorn and Apache

### 31.6 Common Apache Gotchas

### 31.7 Automated, Repeatable Deployments

### 31.8 Which Automation Tool Should Be Used?

### 31.9 Current Infrastructure Automation Tools

### 31.10 Other Resources

### 31.11 Summary

## 32 Continuous Integration

### 32.1 Principles of Continuous Integration

### 32.2 Tools for Continuously Integrating Your Project

### 32.3 Continuous Integration as a Service

### 32.4 Additional Resources

### 32.5 Summary

## 33 The Art of Debugging

### 33.1 Debugging in Development

### 33.2 Debugging Production Systems

### 33.3 Feature Flags

### 33.4 Summary

## 34 Where and How to Ask Django Questions

### 34.1 What to Do When You’re Stuck

### 34.2 How to Ask Great Django Questions in IRC

### 34.3 Feed Your Brain

### 34.4 Insider Tip: Be Active in the Community

### 34.5 Summary

## 35 Closing Thoughts


## Book Review

这是一本关于Django的最佳实践的纲要型的技术书，不仅仅涉及Django而且涉及到了Web开发需要注意的方方面面。每一章都可以单独地作为一个相关主题来进行索引阅读，并且作者给出了极为丰富的相关参考资料。推荐任何读完了Django的tutorial的人都应该至少来阅读一下这本书的大纲，然后再后面涉及到相关问题时先阅读一下本书所提到的建议！
