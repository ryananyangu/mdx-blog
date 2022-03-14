---
title: 'Find Nearest Cordinates between 2 points'
date: '2022-01-11'
tags: ['jwt', 'java', 'spring']
draft: false
summary: 'Find nearest cordinates between 2 point input in .'
---

# NEAREST POINTS/CORDINATES

## Assumptions

- First point in list of tuples is x cordinate
- Second point in the list of tuples is y cordinate
- Uploaded points are grouped batchwise (each api request corresponds to a unique batch)
- Shortest distance considers bidirrectional movement repetion of some points in different order

## Features Implemented in the application

- [x] Application/api security (JWT token generation)
- [x] Data normalizations (Database functionlity)
- [x] Auditable data (Timestamps and user profile on any data manupilation done)
- [x] Application testability (Unit tests to achieve coverage of < 90%)
- [x] Application documentation (Feature and code documentation on readme file)
- [x] Code versioning (Git hub)
- [x] Deployment and delivery via Heroku
- [x] Depandencies management and documentation on requirements.txt
- [x] Logging and log management

## Explicit Tasks

- [x] Create a Django application.
- [x] The application should have an API endpoint that accepts a string of comma separated
      points for example “(2,3), (1,1), (5, 4), ...” and calculates the closest points. It then stores them
      in a table with the following details:
- The string of points submitted
- The result of the computation: the closest points pair
- [x] Write unit tests to validate your expectations
- [x] Host the application on Heroku (or a different service of choice that will be publicly
      available)
- [x] Host your code on GitHub and share repository
- [x] Also share the API of the application and any credentials that might be needed alongside a [Shared on email]
- [x] summary of how your application works

## Application setup

Get the application source code from github

```bash
git clone https://github.com/ryananyangu/tmpmfsrepo.git
```

Change directory into the application directory

```bash
cd tmpmfsrepo
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
gunicorn --bind 0.0.0.0:$PORT mfs.wsgi:application --log-file -
```

Running test and getting the test coverage report.

```
coverage run --source='.' manage.py test cordinates
coverage report

Name                                    Stmts   Miss  Cover
-----------------------------------------------------------
cordinates/__init__.py                      0      0   100%
cordinates/admin.py                         3      0   100%
cordinates/apps.py                          4      0   100%
cordinates/migrations/0001_initial.py       7      0   100%
cordinates/migrations/__init__.py           0      0   100%
cordinates/models.py                       15      0   100%
cordinates/tests.py                        23      0   100%
cordinates/views.py                        72      1    99%
manage.py                                  12      2    83%
mfs/__init__.py                             0      0   100%
mfs/asgi.py                                 4      4     0%
mfs/settings.py                            36      1    97%
mfs/urls.py                                 8      0   100%
mfs/wsgi.py                                 4      4     0%
-----------------------------------------------------------
TOTAL                                     188     12    94%
```

## APIs Implementation and Examples

### Login API

[login link](https://ashiruma-mfs.herokuapp.com/api-token-auth/)

Sample request via curl

```bash
curl -X POST \
  https://ashiruma-mfs.herokuapp.com/api-token-auth/ \
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

[token verification link](https://ashiruma-mfs.herokuapp.com/api-token-verify/)

Sample request

```bash
curl -X POST \
  https://ashiruma-mfs.herokuapp.com/api-token-refresh/ \
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

[token refresh link](https://ashiruma-mfs.herokuapp.com/api-token-refresh/)

Sample request

```bash
curl -X POST \
  https://ashiruma-mfs.herokuapp.com/api-token-refresh/ \
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

### Cordinates check API

[points/ cordinates check link](https://ashiruma-mfs.herokuapp.com/cordinates/check/)

Sample request via curl

```bash
curl -X POST \
  https://ashiruma-mfs.herokuapp.com/cordinates/check/ \
  -H 'authorization: Bearer XXXXXXXXXXXXXXXXXXXXX' \
  -H 'cache-control: no-cache' \
  -H 'content-type: application/json' \
  -d '"(1,2),(3,4),(14,2)"'
```

Sample response
Cordinate pairs that have the shortest distance with the distance value

```json
{
  "cordinates": "(1, 2),(3, 4)|(3, 4),(1, 2)",
  "distance": 2.8284271247461903
}
```

### Admin Portal url

[Administration portal link](https://ashiruma-mfs.herokuapp.com/admin/)
