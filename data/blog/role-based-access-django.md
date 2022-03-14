---
title: 'Role Based Access with Django'
date: '2022-02-14'
tags: ['jwt', 'python', 'django', 'api']
draft: false
summary: 'Provides restrictive access to the users in a system based on their role (User groups) Above implies the entire format will be implemented with group and no granular permissions assigned to the user directly'
cover_image: '/images/posts/access.jpg'
---

# Role Based Access Control

Provides restrictive access to the users in a system based on their role (User groups)
Above implies the entire format will be implemented with group and no granular permissions assigned to the user directly

## Explicit Features/Tasks

- [x] User is a member of one group or more groups
- [x] Group contains many users and many permisions
- [x] Each user created will have a group with their username
- [x] Sub users will be registered on parent users group and they will be able to cascade the permisions downstream.
- [x] Adding permission to the role/Group
- [x] Remove permission from the group/role
- [x] Add user to group/role
- [x] Remove user from role/group
- [x] Get all permisions of a role
- [ ] Get all users of a role

## Features Implemented in the application

- [x] Application/api security (JWT token generation)
- [x] Data normalizations (Database functionlity)
- [x] Auditable data (Timestamps and user profile on any data manupilation done)
- [ ] Application testability (Unit tests to achieve coverage of < 90%)
- [x] Application documentation (Feature and code documentation on readme file)
- [x] Code versioning (Git hub)
- [x] Deployment and delivery via Heroku
- [x] Depandencies management and documentation on requirements.txt
- [x] Logging and log management.

### Potential challenges

- How to handle deletion of parent sub. so that the other subs remain but permision related to the parent sub are removed.
  NOTE: Group defines the role of the user in the system

### Research material

- <https://simpleisbetterthancomplex.com/tutorial/2018/01/18/how-to-implement-multiple-user-types-with-django.html>
- <https://hashedin.com/blog/configure-role-based-access-control-in-django/>
- <https://bootcamp.uxdesign.cc/designing-roles-and-permissions-ux-case-study-b1940f5a9aa>
- <https://hackernoon.com/configure-role-based-access-control-in-django-74fa94a54aff>
- <https://www.django-rest-framework.org/api-guide/permissions/>

### Application setup

Get the application source code from github

```bash
git clone https://github.com/ryananyangu/tmpbuupassrepo.git
```

Change directory into the application directory

```bash
cd tmpbuupassrepo
```

Create virtual environment to host the application dependacies

```bash
python3 -m venv env
```

Activate the virtual environment

```bash
source ../env/bin/activate
```

Install application dependancies into your virtual directory

```bash
pip install -r requirements.txt
```

Add the following enviromental variables to be able to run the application smoothly

```bash
# Advisable never to use the default even though it exists esp in prod
export SECRET_KEY={insert your own !}
# Debug only accepts True or False values default is True
export DEBUG=True|False
# Database url if not set defaults to sqlite setup
export DATABASE_URL={insert your own postgresql url}
```

Making the model migrations and running the generated migrations
For both default apps and cordinates app

```bash
python3 manage.py makemigrations
python3 manage.py makemigrations cordinates
python3 manage.py migrate
```

Create a supper user account to be able to access both the apis and admin portal

```bash
python3 manage.py createsuperuser
# Follow the prompt instructions recieved
```

Run the application

```bash
python3 manage.py runserver
```

Production setup is different as it uses

1. Whitenoise to server static files and
2. Gunicorn for serving the backend api

Following is the variation of production command for running the application
Setup for whitenoise for deployment to heroku is already done in the settings.py file

```bash
# Collect all the static files to be served with application i.e. admin, rest_api browser etc.
python3 manage.py collectstatic --noinput

# Run the application on production
gunicorn --bind 0.0.0.0:$PORT buupass.wsgi:application --log-file -
```

Running test and getting the test coverage report.

```bash
coverage run --source='.' manage.py test cordinates
coverage report
```

#### APIs Implementation and Examples

### Login API

[login link](https://ashiruma-buupass.herokuapp.com/api-token-auth/)

Sample request via curl

```bash
curl -X POST \
  https://ashiruma-buupass.herokuapp.com/api-token-auth/ \
  -H 'cache-control: no-cache' \
  -H 'content-type: application/json' \
  -d '{
        "username": "rashiruma",
        "password": "XXXXXXXXX"
      }'
```

Sample response

```json
{
  "token": "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX"
}
```

### Verify Token API

[token verification link](https://ashiruma-buupass.herokuapp.com/api-token-verify/)

Sample request

```bash
curl -X POST \
  https://ashiruma-buupass.herokuapp.com/api-token-refresh/ \
  -H 'cache-control: no-cache' \
  -H 'content-type: application/json' \
  -d '{
        "token": "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX"
      }'
```

Sample response

```json
{
  "token": "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX"
}
```

### Refresh token API

[token refresh link](https://ashiruma-buupass.herokuapp.com/api-token-refresh/)

Sample request

```bash
curl -X POST \
  https://ashiruma-buupass.herokuapp.com/api-token-refresh/ \
  -H 'cache-control: no-cache' \
  -H 'content-type: application/json' \
  -d '{
        "token": "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX"
      }'
```

Sample response with new token

```json
{
  "token": "YYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYY"
}
```

### GET Roles of logged in user

[get roles of logged in user](https://ashiruma-buupass.herokuapp.com/user/roles/)

```bash
curl --location --request GET 'https://ashiruma-buupass.herokuapp.com/user/roles/' \
--header 'Authorization: Bearer XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX'
```

```json
[
  {
    "id": 1,
    "name": "rashiruma",
    "admin_id__username": "rashiruma",
    "created_at": "2022-01-06T07:08:33.815437Z",
    "created_by__username": "rashiruma",
    "modified_at": "2022-01-06T07:08:33.832000Z",
    "modified_by__username": "rashiruma"
  },
  {
    "id": 3,
    "name": "ztech",
    "admin_id__username": "ztech",
    "created_at": "2022-01-06T10:32:20.586653Z",
    "created_by__username": "ztech",
    "modified_at": "2022-01-06T10:32:20.587621Z",
    "modified_by__username": "ztech"
  }
]
```

### Add user to role

[Add user to a role](https://ashiruma-buupass.herokuapp.com/user/roles/)

```bash
curl --location --request POST 'https://ashiruma-buupass.herokuapp.com/user/roles/' \
--header 'Authorization: Bearer XXXXXXXXXXXXXXXXXXXXXXXXX' \
--header 'Content-Type: application/json' \
--data-raw '{
    "role_id":1,
    "user_id":3
}'
```

Sample response

```json
{
  "message": "user ranyangu added to role rashiruma successfully"
}
```

### Delete user from role

[Remove user from role](https://ashiruma-buupass.herokuapp.com/user/roles/1/)

```bash
curl --location --request DELETE 'https://ashiruma-buupass.herokuapp.com/user/roles/1/' \
--header 'Authorization: Bearer XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX' \
--data-raw ''
```

Sample response

```json
{
  "message": "Role rashiruma removed from user rashiruma profile."
}
```

### Invite user (Sub registration) to personal role

### Invited user request accept request invited role

### Add remove permisions from group

<!-- Logged in user roles => Permisions in the groups  -->
