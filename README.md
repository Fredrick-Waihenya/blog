# Team-Rio-Django

Backend for Team Rio Written in Django for the Space Ya Tech Project

![Python](https://img.shields.io/badge/Python-14354C?style=for-the-badge&logo=python&logoColor=white)
![Django](https://img.shields.io/badge/Django-092E20?style=for-the-badge&logo=django&logoColor=white)
![postgresql](https://img.shields.io/badge/PostgreSQL-316192?style=for-the-badge&logo=postgresql&logoColor=white)

[![Django CI](https://github.com/SpaceyaTech/Team-Rio-Django/actions/workflows/django.yml/badge.svg?event=push)](https://github.com/SpaceyaTech/Team-Rio-Django/actions/workflows/django.yml)
## Table of contents

- [Overview](#overview)
- [Contributing](#contribution-guide)
- [Commit message](#commit-message-template)
- [Handling Phone Numbers](#phonenumberfield)
- [Authentication](#authentication)
- [Tests](#to-run-and-create-unittests)
- [Admin Site Titles](#changing-the-site-titles)
- [Blog Api](#creating-the-blog-api)
- [Blog Admin](#blog-admin)

## Overview

The SpaceYaTech Content Management system is an open-application that lets users to quickly publish content and share it with ease to their audience. Inspired by existing CMSes like Hashnode, Wordpress, DEV and Joomla, we felt the need to create an African CMS created by young Africans looking to learn by contributing to Open Source. SpaceYaTech opted for a CMS as the debut open source project because of the technicalities involved in creating, maintaining and scaling a CMS. A CMS poses great technical challenges and a great learning opportunity for those looking to grow their tech skills.
For a more detailed overview of the project, read through the [CMS Backend wiki](https://github.com/SpaceyaTech/CMS-Backend-Repository/wiki)

## Contribution guide

Get to read the [Contributions guide](https://github.com/SpaceyaTech/Team-Rio-Django/blob/main/contributions.md) here.

## Commit message template

Just so that we have all our commit messages to be more readable and sensible it is recomended we use a template for the commit messages. Here is a [commit message template](https://github.com/SpaceyaTech/Team-Rio-Django/wiki/Commit-Messages) that one should follow when making your Contributions

## PhoneNumberField

In order to use **PhoneNumber_field** for localization option, perform the following:

- Install phonenumber minimal metadata

```bash
pip install "django-phonenumber-field[phonenumberslite]"
```

or

- Install phonenumber extended features (e.g. geocoding)

```bash
pip install "django-phonenumber-field[phonenumbers]"
```

- Add `phonenumber_field` to the list of the installed apps in your `settings.py` file INSTALLED_APPS:

```python
INSTALLED_APPS = [
    # Other apps…
    "phonenumber_field",
]
```

- Model field section add the following:

```python
from phonenumber_field.modelfields import PhoneNumberField

phone_number = PhoneNumberField(blank=True)
```


## AUTHENTICATION

For authentication you follow this instructions:

- Since we are using djangorest framework. Install the django rest framework library and add it to the INSTALLED_APPS as a third party app.

- Create the serializers by adding a new file in your accounts app and name it serializers.py

- In the serializers file create the needed serializers in this case , UserSerializer, RegisterSerializer, AddAccountSerializer.

- Create the api views in the views.py file in the accounts app. In this case the UserView, RegisterView, AddAccountView

- Create the corresponding urls by creating a urls.py file in your accounts app. Make sure to include your app urls in your projects urls.py file in the following way

```python
from django.urls import path, include

urlpatterns = [
    #Other patterns

    path("", include("accounts.urls"))
]
```

- Install the djangorestframework jwt library

```bash
pip install djangorestframework_simplejwt
```

- In your projects settings.py configure the REST_FRAMEWORK settings to use JWT and set the AUTH_HEADER_TYPE as JWT. For the access token lifetime i've set it to 1 day for testing purposes.

```python
REST_FRAMEWORK = {
    'DEFAULT_AUTHENTICATION_CLASSES': (
        'rest_framework_simplejwt.authentication.JWTAuthentication',
    ),
}

SIMPLE_JWT = {
   'AUTH_HEADER_TYPES': ('JWT',),
   'ACCESS_TOKEN_LIFETIME': timedelta(days=1),
}

```

- In your urls.py add the following:

```python
from rest_framework_simplejwt import views as jwt_views

urlpatterns = [
    #Other patterns

    path('api/token/', jwt_views.TokenObtainPairView.as_view(), name='token_obtain_pair'),
    path('api/token/refresh/', jwt_views.TokenRefreshView.as_view(), name='token_refresh'),
    ]

```

- Add various permissions to your apis in the views.py file.

## To Run and Create unittests

Add your tests in the test file in the test folder which is located in the app you want to test.  
To run all tests  
```python
python3 manage.py test
```
To run tests in a particular app

```bash
python3 manage.py test [appname]
```
## Changing the site titles 
Having the site titles for the project to read **SpacryaTech**
![image](https://user-images.githubusercontent.com/23496280/204856386-3105fb57-a020-47c7-a789-8943099f3e44.png)

## Creating the blog Api

The blog api **{{baseurl}}/blog/**
shows a list of all available blog posts (Stori/Mastori)
The model naming is abitrary and can be subject to change if need be
Ther is also need to filter out the various blogposts in relation ti their tittle or date posted hence the filter

![Screenshot from 2023-01-03 00-59-16](https://user-images.githubusercontent.com/23496280/210282497-96bb8b6f-544d-4454-8b01-e3aea9b8745d.png)

## Blog admin

Here the implementation is more similar to the api but for the admin the search fields are title and content
```python
  search_fields = ['title', 'content']
```
theres also an addition of a slug field this is in anticipation of creation of the detail view 
the slug could be used to generate more elaborate urls for specific blogposts (mastori)
Filtering has also been implemented here by filtering on the basis of the post ststus
```python
 list_filter = ("status",)
 ```
 here a post is either a draft or published

![Screenshot from 2023-01-03 01-00-46](https://user-images.githubusercontent.com/23496280/210282501-cfb7ebf1-c95b-48c2-96dc-407000045a00.png)

## comment model
The Comment model is used to create comments on a particular post. It has the following fields:

body: A text field to enter the comment text.
Post_id: A foreign key to the Stori model to which the comment belongs. This field is required.
user: A foreign key to the Account model which represents the user who created the comment. This field is required.
date_created: A datetime field that stores the date and time when the comment was created. This field is automatically generated by setting the auto_now_add attribute to True.
reactions: A generic relation to the Reaction model which allows users to react to the comment.
parent_comment: A foreign key to the Comment model which represents the parent comment of the current comment. This field is optional and can be left blank. If the current comment has a parent comment, then it is considered a child comment.
ordering: A meta option to order the comments based on the date they were created in descending order.
The Comment model also has two methods:

__str__(self): A method that returns a string representation of the comment by concatenating the comment body and the user's account name.
child_comment(self): A method that returns all the child comments of the current comment.
The Comment model has a property decorator:

is_parent: A property that returns a boolean value indicating whether the comment is a parent comment or not. If the comment has a parent comment, it is not considered a parent comment.
Documentation for Verification Feature:

## User model
The User model is used to create user accounts. It has the following fields:

email: An email field to enter the email address of the user. This field is required and must be unique.
phone_number: A phone number field to enter the contact phone number of the user. This field is optional but must be unique if provided.
verification_code: A character field to store the verification code generated for the user. This field is required, unique and has a default value generated by the generate_verification_code() function.
code_generated_at: A datetime field that stores the date and time when the verification code was generated. This field is automatically generated by setting the auto_now_add attribute to True.
is_verified: A boolean field that indicates whether the user has been verified or not. This field is initially set to False and will be updated in the views when the user is verified.
USERNAME_FIELD: A string field that specifies the name of the field to use as the username field.
REQUIRED_FIELDS: A list of required fields to be filled in during registration, including first_name, last_name, username, and phone_number.
The User model also has the following methods:

__str__(self): A method that returns a string representation of the user by concatenating the user's first name and last name.
__init__(self, *args, region=None, **kwargs): An initializer that takes in the region and sets it to the region that was passed in when the user is created.
get_verification_code(self): A method that resets the verification code after every hour if it has elapsed since the code was generated. It returns the verification code.