#  Setup Django REST Framework


## Author Information

| **Field**       | **Details**                                                                 |
|-----------------|-----------------------------------------------------------------------------|
| Name            | Najmul Islam                                                               |
| Email           | [najmulislamru@gmail.com](mailto:najmulislamru@gmail.com)                  |
| Designation     | Python Developer                                                           |
| Location        | Dhaka, Bangladesh                                                          |
| LinkedIn        | [Najmul Islam](https://www.linkedin.com/in/najmulislamnajimofficial/)      |

##
##  Set Environ
### Install django-environ

```bash
pip  install  django-environ
```
### Import os and environ in project settings
```py
# Settings.py
import os
import environ
```
### Set env
```py
# Settings.py
env = environ.Env(
    # set casting, default value
    DEBUG=(bool, False)
)
```
### Read env variables (write after BASE_DIR)
```py
# Settings.py
environ.Env.read_env(os.path.join(BASE_DIR, '.env'))
```
### Write .env file
```bash
# .env
# Debug
DEBUG=True
# Secret key
SECRET_KEY='your project secret key'
#Defalult DB Config
DEFAULT_DB_HOST=1.1.1.1
DEFAULT_DB_PASSWORD="your password"
DEFAULT_DB_USER=root
DEFAULT_DB_NAME=mydb_name
DEFAULT_DB_PORT=3306
# Allowed Hosts
ALLOWED_HOSTS=localhost,127.0.0.1
```
## 
## Install REST Framework
### Install
```bash
pip install djangorestframework
```
### Add Installed App Section
```py
# Settings.py
INSTALLED_APPS = [
    ...
    'rest_framework',
]
```
##
## Set Documentation
### Install packages
```bash
pip  install  drf-spectacular
```
### Add Installed App section
```py
# Settings.py
INSTALLED_APPS = [
    # ALL YOUR APPS
    'drf_spectacular',
]
```
### Add DRF Settings
```py
# Settings.py
REST_FRAMEWORK = {
    # YOUR SETTINGS
    'DEFAULT_SCHEMA_CLASS': 'drf_spectacular.openapi.AutoSchema',
}
```
### Add Doc Settings
```py
# Settings.py
SPECTACULAR_SETTINGS  =  {
	'TITLE':  'RDL Expire Product API',
	'DESCRIPTION':  'API documentation for project name.\nCreated and developed by Najmul Islam',
	'VERSION':  '1.0.0',
}
```
### Setup URL
```py
# urls.py
from drf_spectacular.views import SpectacularAPIView, SpectacularSwaggerView, SpectacularRedocView

urlpatterns =  [
	# Your existing urls
	path('api/schema/', SpectacularAPIView.as_view(),  name='schema'),
	path('api/v1/docs', SpectacularSwaggerView.as_view(url_name='schema'),  name='swagger-ui'),
	path('api/docs/redoc/', SpectacularRedocView.as_view(url_name='schema'),  name='redoc')
]
```
##
## Setup Logging
### Import Logging
```py
# Settings.py
import logging
```
### Create a Directory if it does not exist
```py
# Settings.py
os.makedirs(BASE_DIR  /  'logs/your_directory_name',  exist_ok=True)
```
### Set Local Date Time
```py
# Settings.py
class  DhakaFormatter(logging.Formatter):
	def converter(self,  timestamp):
		dt = datetime.fromtimestamp(timestamp, pytz.timezone("Asia/Dhaka"))
		return dt
	def formatTime(self,  record,  datefmt=None):
		dt =  self.converter(record.created)
		if datefmt:
			return dt.strftime(datefmt)
		return dt.isoformat()
```

### Set custom Filter
```py
# Settings.py
class  LevelFilter(logging.Filter):
	def __init__(self,  level):
		super().__init__()
		self.level = level
	def filter(self,  record):
		record.levelname =  self.level
		return record.levelno ==  self.level
```
### Set Logging

```py
# Settings.py
LOGGING  =  {
	'version':  1,
	'disable_existing_loggers':  False,
	'formatters':  {
		'standard':  {
			'()': DhakaFormatter,
			'format':  '{levelname}  {name}  {lineno}  {asctime}  {filename}  {funcName}  {message}',
			'style':  '{',
			'datefmt':  '%d-%m-%Y %H:%M:%S'
		},
		'simple':  {
			'()': DhakaFormatter,
			'format':  '{levelname}  {lineno}  {asctime}  {filename}  {funcName}  {message}',
			'style':  '{',
			'datefmt':  '%d-%m-%Y %H:%M:%S'
		}
	},
	'filters':{
		'info_only':{
			'()': LevelFilter,
			'level': logging.INFO,
		},
		'error_only':{
			'()': LevelFilter,
			'level': logging.ERROR,
		},
		'critical_only':{
			'()': LevelFilter,
			'level': logging.CRITICAL,
		},
	},
	'handlers':  {
		'console':  {
			'class':  'logging.StreamHandler',
		},
		'appName_info':  {
			'level':  'INFO',
			'class':  'logging.handlers.RotatingFileHandler',
			'filename':  BASE_DIR  /  'logs/appName/info.log',
			'formatter':  'standard',
			'filters':  ['info_only'],
			'maxBytes':  1024  *  1024  *  5,  # 5 MB
			'backupCount':  5,
		},
		'appName_error':  {
			'level':  'ERROR',
			'class':  'logging.handlers.RotatingFileHandler',
			'filename':  BASE_DIR  /  'logs/appName/error.log',
			'formatter':  'standard',
			'filters':  ['error_only'],
			'maxBytes':  1024  *  1024  *  5,  # 5 MB
			'backupCount':  5,
		},
		'appName_critical':  {
			'level':  'CRITICAL',
			'class':  'logging.handlers.RotatingFileHandler',
			'filename':  BASE_DIR  /  'logs/appName/critical.log',
			'formatter':  'standard',
			'filters':  ['critical_only'],
			'maxBytes':  1024  *  1024  *  5,  # 5 MB
			'backupCount':  5,
		},
	},
	'loggers':{
		'':  {
			'handlers':  ['console'],
			'level':  'DEBUG',
			'propagate':  False
		},
		'appName':  {
			'handlers':  ['appName_info',  'appName_error',  'appName_critical'],
			'level':  'INFO',
			'propagate':  False
		},
	}
}
```
##
## Set Static and Media URL Path
```py
#Settings.py
STATIC_URL  =  'static/'
STATIC_ROOT  =  BASE_DIR  /  "static"
MEDIA_URL  =  '/media/'
MEDIA_ROOT  =  BASE_DIR  /  'media'
```
```py
#urls.py
from django.contrib import admin
from django.conf import settings
from django.conf.urls.static import static
from django.urls import path, include

urlpatterns =  [
	# your path
]  +  static(settings.MEDIA_URL,  document_root=settings.MEDIA_ROOT)
```
##
