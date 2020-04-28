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
