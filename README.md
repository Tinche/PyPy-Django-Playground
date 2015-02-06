# Setup Instructions

Create two virtualenvs, side by side. Substitute your own path for PyPy3. :)

```
virtualenv --python=/home/tin/Downloads/pypy3-2.4.0-linux64/bin/pypy3 .venv-pypy3
virtualenv --python=python3 .venv-py3
.venv-pypy3/bin/pip install -U setuptools pip
.venv-pypy3/bin/pip install -r requirements.txt

.venv-py3/bin/pip install -U setuptools pip
.venv-py3/bin/pip install -r requirements.txt

.venv-pypy3/bin/python manage.py collectstatic --noinput
```

# Running

The settings are set up to enable debug mode if an environment variable, `DEBUG`, is defined.

## Django's runserver in debug mode

```
DEBUG=true .venv-pypy3/bin/python manage.py runserver
DEBUG=true .venv-py3/bin/python manage.py runserver
```

## Django's runserver in non-debug mode

```
.venv-pypy3/bin/python manage.py runserver
.venv-py3/bin/python manage.py runserver
```

## Gunicorn, single process, non-debug

```
.venv-pypy3/bin/gunicorn pypy_django.wsgi -w 1
.venv-py3/bin/gunicorn pypy_django.wsgi -w 1
```

# Benchmarking

I use Apache's Benchmark tool (http://httpd.apache.org/docs/2.4/programs/ab.html). It can be installed in Ubuntu with `sudo apt-get install apache-utils`.

The SQLite database has a session pre-inserted. This session can be used to request the Django admin index page.

```
ab -n 100 -c 1 -C "sessionid=03ak15mg4h2xcza6jjzmcxwftm4wfjbe" http://localhost:8000/admin/
```

Sample results:

```
This is ApacheBench, Version 2.3 <$Revision: 1604373 $>
Copyright 1996 Adam Twiss, Zeus Technology Ltd, http://www.zeustech.net/
Licensed to The Apache Software Foundation, http://www.apache.org/

Benchmarking localhost (be patient).....done


Server Software:        gunicorn/19.2.1
Server Hostname:        localhost
Server Port:            8000

Document Path:          /admin/
Document Length:        4456 bytes

Concurrency Level:      1
Time taken for tests:   2.299 seconds
Complete requests:      100
Failed requests:        0
Total transferred:      479000 bytes
HTML transferred:       445600 bytes
Requests per second:    43.49 [#/sec] (mean)
Time per request:       22.993 [ms] (mean)
Time per request:       22.993 [ms] (mean, across all concurrent requests)
Transfer rate:          203.44 [Kbytes/sec] received

Connection Times (ms)
              min  mean[+/-sd] median   max
Connect:        0    0   0.0      0       0
Processing:    19   23   8.2     22     100
Waiting:       19   22   8.2     21     100
Total:         20   23   8.2     22     100

Percentage of the requests served within a certain time (ms)
  50%     22
  66%     22
  75%     22
  80%     23
  90%     24
  95%     27
  98%     39
  99%    100
 100%    100 (longest request)
```
