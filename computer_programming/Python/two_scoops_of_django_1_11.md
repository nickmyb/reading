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
