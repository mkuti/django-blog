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
> to serve out media files, add new context processor under OPTIONS: django.template.context_processors.media
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
1. create folder for templates at root level
2. create base.html
3. {% load static from staticfiles %} before DOCTYPE
4. add doctype and boilerplate
5. add links to custom css href="{% url 'css/custom.css %}" and bootstrap cdn 
6. add {% block content %} and {% endblock %} inside body after navbar
7. in navbar, for each nav-item, add {% url 'get_posts' %} or {% url 'new_post' %} in href 
8. inside templates folder of posts app, add blogposts.html file
9. add {% extends 'base.html' %} {% block content %}
10. {% for post in posts %}
11. create html structure and in h3 element, add {{ post.title }}
12. in p element, add {{ post.content|truncatewords:30 }} brief overview of post contents
13. add url below to go and get more details: {% url 'post_detail' post.id %}
14. {% endfor %}
15. {% endblock %}
16. create new file called post_detail.html
17. add {% extends 'base.html' %} {% block content %}
18. {% if post.image %} >> have img element with src='/media/{{ post.image }}' >> {% endif %}
19. in h3 element, add {{ post.title }}, {{ post.content }} inside p element, etc...
20. add 2 buttons at the bottom: Back to Blog <a href="{% url 'get_posts' %}"> and Edit post <a href=" {% url 'edit_post' post.id %}">
21. pip3 install django-forms-bootstrap
22. add django_forms_bootstrap inside settings.py under INSTALLED_APPS
23. Create new file blogpostform.html
24. add {% extends 'base.html' %}
25. add {% load bootstrap_tags %}
26. add {% block content %}
27. create form with method of POST and enctype="multipart/form-data"
28. add {% csrf_token %}
29. add {{ form | as_bootstrap }}
30. add submit button to save

## CREATE SUPERUSER
1. python3 manage.py createsuperuser
memememe


## CREATE HEROKU APP 
1. Create env.py with os.environ.setdefault('SECRET_KEY', 'secret-key-here')
2. add env.py to .gitignore
3. in settings.py, add >> if os.path.exists('env.py'): import env
4. SECRET_KEY = os.environ.get('SECRET_KEY')
5. Create app in Europe on heroku dashboard
6. Add add-ons, Heroku Postgres database
7. Add SECRET_KEY environment variable on heroku settings
8. On terminal, pip3 install dj-database-url psycopg2
>> libraries allowing us to connect to database using url instead of standard database driver used by Django
9. pip3 freeze > requirements.txt
10. Update settings.py to use new database url 
11. comment out default databases
12. add DATABASES = {'default': dj_database_url.parse(os.environ.get('DATABASE_URL'))}
13. import dj_database_url
14. Add database url in env.py for running app locally, copy and paste database_url from heroku
15. os.environ.setdefault('DATABASE_URL', 'add_database_url_here')
16. python3 manage.py migrate to move all tables to new heroku database
17. still need to import data from django database to heroku database

## HOST OUR STATIC FILES
>> Whitenoise is a package that helps us host our static files on Heroku, allows us to host our CSS and JavaScript files on Heroku
1. pip3 install whitenoise
2. pip3 freeze > requirements.txt
3. add in settings.py: 'whitenoise.middleware.WhiteNoiseMiddleware' under MIDDLEWARE
4. add STATIC_ROOT = os.path.join(BASE_DIR, 'staticfiles') at the bottom of settings.py
>> gives a collection point for our static files. When we deploy to Heroku, going to collect those into that location and deploy them into project.
5. since we are not sending env.py to repository or Heroku, so Travis testing wont know the database url
6. add if statement: <if 'DATABASE_URL' in os.environ: > >> use Heroku database stored in env.py
7. else, use sqlite3 database
>> if DATABASE_URL is in our environment, then we use it, if not, then we use SQLite instead which is already uploaded to github
8. Create Procfile and add inside: web: gunicorn blog:wsgi:application
9. pip3 install gunicorn
10. pip3 freeze > requirements.txt
11. add 'mapi-django-blog.herokuapp.com' to ALLOWED_HOSTS