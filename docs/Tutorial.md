quick start:

first of all,you can create a file called `app.py` 

then write down:

```

from jolla import WebApp,jolla_server,render

def index(request):
    return render('index.html')

class app(WebApp):
    urls=[
        (r'/',index)
    ]

if __name__=="__main__":
    server=jolla_server(app)
    server.run_server()

```

after you have already written the code in the same path , create a folder called "templates",in that folder,create a HTML file called `index.html`.

the basic path:

```

.
├── app.py
└── templates
    └── index.html

```

In the HTML file `index.html` , write down:

```

<html>

<head>
  <title>Index</title>
</head>

<body>
  <h1>Hello world</h1>
</body>

</html>

```

In your terminal , run:

    python app.py


If you have `httpie` , a package like `curl` ,run `http http://127.0.0.1:8000` in the terminal , then you can see:

```

HTTP/1.1 200 OK
Content-Length: 94
Content-Type: text/html
Date: Fri, 06 May 2016 12:02:20 GMT
Server: Jolla/1.0

<html>

<head>
  <title>Index</title>
</head>

<body>
  <h1>Hello world</h1>
</body>

</html>

```

The basic code done!

******

#JSON Response

Above is just a simple web page.

`Jolla` means to be a `API server` .

we change our `app.py` file like this:

```

from jolla import WebApp, jolla_server, render, render_json


def index(request):
    return render('index.html')


def status(request):
    aljun = {'name': 'aljun', 'education': {
        'college': 'BUCT', 'subject': 'Chemistry'}, 'age': '20'}
    return render_json(aljun)


class app(WebApp):
    urls = [
        (r'/', index),
        (r'/status', status)
    ]

if __name__ == "__main__":
    server = jolla_server(app)
    server.run_server()

```

run `http http://127.0.0.1:8000/status` in our terminal

we can see:

```

HTTP/1.1 200 OK
Content-Length: 95
Content-Type: application/json
Date: Fri, 06 May 2016 12:10:36 GMT
Server: Jolla/1.0

{
    "age": "20", 
    "education": {
        "college": "BUCT", 
        "subject": "Chemistry"
    }, 
    "name": "aljun"
}

```

******

#Route System

We can see , the route we written down is totally fixed.for some place it's hard to use,for example,if we have 1000 url,we have to write down 1000 route rules.

In `Jolla` ,we can try to write down the route like `r/user/<id>`,then we can get the route like `/user/1` or `/user/aljun` . and the `id` has been a parameter in the `request`.

all right,let's try it:

```

from jolla import WebApp, jolla_server, render, render_json


def index(request):
    return render('index.html')


def status(request):
    aljun = {'name': 'aljun', 'education': {
        'college': 'BUCT', 'subject': 'Chemistry'}, 'age': '20'}
    return render_json(aljun)


def user(request):
    user = request['id']
    return render_json({'user': user})


class app(WebApp):
    urls = [
        (r'/', index),
        (r'/status', status),
        (r'/user/<id>', user)
    ]

if __name__ == "__main__":
    server = jolla_server(app)
    server.run_server()

```

run `http http://127.0.0.1:8000/user/aljun` , we can see that:

```

HTTP/1.1 200 OK
Content-Length: 19
Content-Type: application/json
Date: Fri, 06 May 2016 12:25:06 GMT
Server: Jolla/1.0

{
    "user": "aljun"
}


```

Now we have a strong and free route system.

******

#HTTP Status Code

We know there are a lots of HTTP status codes like `GET` or `POST`.and a server need to get value from the client end.

all of that contain in `Jolla`

we can change our `app.py`:

```

from jolla import WebApp, jolla_server, render, render_json


def index(request):
    return render('index.html')


def status(request):
    aljun = {'name': 'aljun', 'education': {
        'college': 'BUCT', 'subject': 'Chemistry'}, 'age': '20'}
    return render_json(aljun)


def user(request):
    user = request['id']
    return render_json({'user': user})


def post(request):
    if request['method'] == 'GET':
        return render_json({'method': 'GET', 'data': request['data']})
    if request['method'] == 'POST':
        return render_json({'method': 'POST', 'data': request['data']})


class app(WebApp):
    urls = [
        (r'/', index),
        (r'/status', status),
        (r'/user/<id>', user),
        (r'/post', post)
    ]

if __name__ == "__main__":
    server = jolla_server(app)
    server.run_server()

```

Use the `requests` lib to try this:

```

In [1]: import requests

In [2]: r=requests.get('http://127.0.0.1:8000/post',data={'name':'Jolla'})

In [3]: r.text
Out[3]: u'{\n"data": {\n"name": "Jolla"\n}, \n"method": "GET"\n}'

In [4]: r=requests.post('http://127.0.0.1:8000/post',data={'name':'Jolla','qqq':'www'})

In [5]: r.text
Out[5]: u'{\n"data": {\n"qqq": "www", \n"name": "Jolla"\n}, \n"method": "POST"\n}'

```

we can see that,the value from client end would be contained `request['data']`,it would be the `dict` structure.

******

#Request parameter

let's focus on some details,what are contained in the parameter `request`

we change `app.py` like this:

```

from jolla import WebApp, jolla_server, render, render_json


def index(request):
    return render('index.html')


def status(request):
    aljun = {'name': 'aljun', 'education': {
        'college': 'BUCT', 'subject': 'Chemistry'}, 'age': '20'}
    return render_json(aljun)


def user(request):
    user = request['id']
    return render_json({'user': user})


def post(request):
    if request['method'] == 'GET':
        return render_json({'method': 'GET', 'data': request['data']})
    if request['method'] == 'POST':
        return render_json({'method': 'POST', 'data': request['data']})

def detail(request):
    return render_json({'request':request})


class app(WebApp):
    urls = [
        (r'/', index),
        (r'/status', status),
        (r'/user/<id>', user),
        (r'/post', post),
        (r'/detail',detail)
    ]

if __name__ == "__main__":
    server = jolla_server(app)
    server.run_server()

```

we run `http http://127.0.0.1:8000/detail` in our terminal.we can see that:

```

HTTP/1.1 200 OK
Content-Length: 289
Content-Type: application/json
Date: Fri, 06 May 2016 12:45:45 GMT
Server: Jolla/1.0

{
    "request": {
        "content_length": null, 
        "content_type": null, 
        "cookies": null, 
        "data": {}, 
        "http_accept_encoding": null, 
        "http_connect": "keep-alive", 
        "http_port": "127.0.0.1:8000", 
        "http_protocol": "HTTP/1.1", 
        "method": "GET", 
        "query_string": {}, 
        "user_agent": "HTTPie/0.9.3"
    }
}

```

from above, we can see,the request contain many environment parameter for us to use conveniently.

******

#Adding Header

we all know the use of the HTTP Header.so if we can customize our logic.

change our `app.py` like:

```

from jolla import WebApp, jolla_server, render, render_json


def index(request):
    return render('index.html')


def status(request):
    aljun = {'name': 'aljun', 'education': {
        'college': 'BUCT', 'subject': 'Chemistry'}, 'age': '20'}
    return render_json(aljun)


def user(request):
    user = request['id']
    return render_json({'user': user})


def post(request):
    if request['method'] == 'GET':
        return render_json({'method': 'GET', 'data': request['data']})
    if request['method'] == 'POST':
        return render_json({'method': 'POST', 'data': request['data']})

def detail(request):
    return render_json({'request':request})

def header(request):
    return render('index.html',extra_header=[('Vary','Accept-Encoding'),('X-Powered-By','PHP 5.4.28')])


class app(WebApp):
    urls = [
        (r'/', index),
        (r'/status', status),
        (r'/user/<id>', user),
        (r'/post', post),
        (r'/detail',detail),
        (r'/header',header)
    ]

if __name__ == "__main__":
    server = jolla_server(app)
    server.run_server()

```

run `http http://127.0.0.1:8000/header` in our terminal , we can see:

```

HTTP/1.1 200 OK
Content-Length: 94
Content-Type: text/html
Date: Fri, 06 May 2016 12:53:58 GMT
Server: Jolla/1.0
Vary: Accept-Encoding
X-Powered-By: PHP 5.4.28

<html>

<head>
  <title>Index</title>
</head>

<body>
  <h1>Hello world</h1>
</body>

</html>

```
ok,we can see,the header has already been added.

******
#session

At this time,we wanna to have our data from client end too exist long,so it's time for us to have `session`,but here,I recommand you to use such as `redis` to be your session choice.`jolla` has its own session module,and it contain `empty`,`session_count`,`add_value`,`check_value`,`del_value`,`get_value` these API for you to use.

Like:

```

def blog(request):
    if request['method']=="POST":
        session.add({'login_username':request['data']['username']})
        return redirect("/")
    if request['method']=='GET':
        if session.check_value(request['data']['username']):
            return redirect("/loged")
        else:
            return redirect("/register")
```
******
#Deployment(logging and host and port)

sometimes,we want our log to be written in a file instead of strout.so how can we do that in `Jolla`?

it just so easy,you just need to add a parameter:

```
if __name__ == "__main__":
    server = jolla_server(app,log="errer.log")
    server.run_server()
```

like that,when we run our program,we can see our log through the `error.log` file

In the terminal,we run `tail error.log`:

then ,we can see:

```
[05-26-2016 14:14:55] INFO:127.0.0.1 - - [2016-05-26 14:14:55] "GET / HTTP/1.1" 200 214 0.001587
[05-26-2016 14:14:58] INFO:127.0.0.1 - - [2016-05-26 14:14:58] "GET / HTTP/1.1" 200 214 0.002000
[05-26-2016 14:15:02] WARNING:<REQUEST /qq/ NOT FOUND IN ROUTE CONFIGURATION>
[05-26-2016 14:15:02] INFO:127.0.0.1 - - [2016-05-26 14:15:02] "GET /qq HTTP/1.1" 404 140 0.000875
```

If we wanna deploy our application,it can not be running in the `localhost`,and to change it in `Jolla` is also simple,`jolla` contain a `listener` object,you can just send the `host` and the `port` to it in order

```
if __name__ == "__main__":
    server = jolla_server(app,log="errer.log",listener=("xx.xx.xx.xx",80))
    server.run_server()
```

like this, our jolla application is running on `xx.xx.xx.xx:80`

******
#multi process

Now,it is all the multi core CPU,so if you can not make full use of multi core cpu,you can not get the best power you want.`jolla` add the multi process function to support that.

it is based on the `python's multiprocessing` module

> recommand:the number of processes equal to the number of your CPU core is the best.

 

```

from jolla import SessionError,HTTP404Error,session,plugins,plugins
from gevent.server import _tcp_listener
from multiprocessing import Process, current_process, cpu_count

session = session()


def index(request):

    return plugins.render('indexwww.html',extra_header=[('Vary','Accept-Encoding'),('X-Powered-By','PHP 5.4.28')])

def say(request):

    return plugins.render_json({'qqq':{'www':'哈哈哈'}},indent=4)


class app(server.WebApp):
    urls = [
        (r'/', index),
        (r'/data', say)
    ]
listener = _tcp_listener(('127.0.0.1', 8001))

def serve_forever(listener):
    server.jolla_server(listener=listener, app=app).run_server()

if __name__ == '__main__':
    number_of_processes = 4
    print 'Starting %s processes' % number_of_processes
    for i in range(number_of_processes):
        Process(target=serve_forever, args=(listener,)).start()
```

after I test the benchmark of that.I use my mac ,and the 3 process is twice faster than i process.

******

Hope you guys like it.

