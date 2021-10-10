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
