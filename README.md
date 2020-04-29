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
__published_date__ DateTimeField which is blank at first, where null are allowed, default is current time from timezone.now
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

## Migrations
1. pip3 install pillow library for images and add it to requirements
2. python3 manage.py makemigrations
3. python3 manage.py migrate

## CREATE VIEWS 
1. inside views.py, from django.shortcuts import render, get_object_or_404, redirect
2. from django.utils import timezone
3. from .models import Post
4. from .forms import BlogPostForm
5. create 3 different views with docstring to explain each view
6. 1st view: to get posts created before now: 
- posts = Post.objects.filter__lte >> filter all our posts on published date less than or equal to timezone.now. __lte = less than equal to
- order the filtered results by published date on descending order
- return render blogposts.html with posts as passing argument
7. 2nd view: return 1 single post based on post ID and adding a view to the post
- post = get_object_or_404(Post, pk=pk)
- post.views += 1 and post.save()>> increment views by one and save post with added view
- return render postdetail.html with {'post': post} as passing argument
8. 3rd view: create or edit post
- create post object from get_object_or_404(Post, pk=pk) if pk exists, else return None
- if form is posted, we want to have a look at the result of the form: form = BlogPostForm(request.POST, request.FILES, instance=post)
- if the form is valid, we save it and we redirect back to our post_detail
- if request.method is get, returning the BlogPostForm to be filled by user
- return render blogpostform.html with form object as passing argument

## CREATE URLS IN PROJECT
1. in urls.py
- add include on import list from django.conf.urls >> allow us to include extra URL files
- from django.views.generic import RedirectView >> allow us to redirect to a view
- from django.views.static import serve
- from .settings import MEDIA_ROOT >> we will serve out our media URL
2. create 1st url for root directory of our project RedirectView.as_view(url='posts/')) >> redirect user to post view
3. create 2nd url for posts which is appended by urls in the urls.py in posts app
4. create 3rd url for media serve: url(r'^media/(?P<path>.*)$', serve, {'document_root': MEDIA_ROOT})

## CREATE URLS IN APP 
1. create new file urls.py in posts app
2. from django.conf.urls import url
3. from .views import all views
4. urlpatterns = []
5. 1st url: get_posts name, on root level of post, which will be returned on /posts url
6. 2nd url: post_detail name, r'^(/P<pk>\d+)/$, posts/id
7. 3rd url: new_post name
8. 4th url: edit_post name, r'^(/P<pk>\d+)/edit/$, post/id/edit

## CREATE TEMPLATES WITH CSS 
