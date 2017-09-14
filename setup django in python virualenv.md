# install python environment
```
# install pip for python2.7
apt-get install python-pip

# or pip3 for python3.x
apt-get install python3-pip

# install virtualenv tools
sudo apt-get install python-virtualenv

# init virtualenv
virtualenv ENV

# active ENV
source ./ENV/bin/activate

# if you want exit
deactivate
```

# install django
```
# install django 1.11
pip install django==1.11
```

# install celery
```
# install celery
pip install celery

# Using the Django ORM/Cache as a result backend
pip install django-celery-results
```

# install redis
```
pip install -U "celery[redis]"
```

# config redis in settings.py
```
# configure the location of your Redis database:
# where the URL in the format of redis://:password@hostname:port/db_number
app.conf.broker_url = 'redis://localhost:6379/0'

# If you also want to store the state and return values of tasks in Redis, you should configure these settings:
app.conf.result_backend = 'redis://localhost:6379/0' 

# Visibility Timeout
app.conf.broker_transport_options = {'visibility_timeout': 3600}  # 1 hour
```

