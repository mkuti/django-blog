# Django Blog Mini-Project

A simple blog app written using Django


[![Build Status](https://travis-ci.org/mkuti/django-blog.svg?branch=master)](https://travis-ci.org/mkuti/django-blog)

1. pip3 install django==1.11.28
2. pip3 freeze > requirements.txt
3. django-admin startproject blog .
4. echo -e "*.sqlite3\n*.pyc\n__pycache__/" > .gitignore
5. go to travisci.org and sign in with github
6. toggle repo to sync with github
7. in unknown build, change format to markdown, copy result text to README.md 
8. django-admin startapp posts
9. Create templates directory inside app
10. Create media directory and img subfolder at root level
11. Create static direcory at root level and inside, add css, img and js subfolders
12. in settings.py 
> add app in INSTALLED_APPS
> add 'DIRS': [os.path.join(BASE_DIR, 'templates')], inside templates
> to serve out media files, add new context processor under OPTIONS
    >> STATIC_URL = '/static/' and STATICFILES_DIRS = (os.path.join(BASE_DIR, 'static'), )
    >> MEDIA_URL = '/media/' and MEDIA_ROOT = os.path.join(BASE_DIR, 'media')    
13. add 'localhost' to ALLOWED_HOSTS

## MODELS AND FORMS
1. in models.py from django.util import timezone
2. create class Post(models.Model) base on standard models
3. create all fields
__title__ CharField
__content__ simple text box
__created_date__ DateTimeField where current time and date is automatically added when record is created 
__published_date__ DateTimeField which is blank at first, where null are allowed, default is current time from timezone
__views__ record number of views for post with IntegerField and with default value of 0 before any post is created
__tag__ to tag posts together depending of themes, CharField, blank at first and null are allowed 
__image__ ImageField, upload_to corresponds to directory created under media, img 
4. add function after to return self.title 
5. create new file forms.py
6. from django import forms
7. from .models import Post -- import Post class from model file
8. create class BlogPostForm(forms.ModelForm) using Django forms library
9. inner class Meta to describe only the user editable fields and the model used

## Making sure posts are accessible through admin backend
1. in admin.py, import Post class from models.py
2. add admin.site.register(Post)