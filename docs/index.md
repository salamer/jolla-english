![pic](http://7xlen8.com1.z0.glb.clouddn.com/JOLLA%281%29.png)
#Welcome to Jolla

******

###Jolla is a simple and fast API server written in Python 2.7.

`Jolla` is based on `gevent's pywsgi`. because `gevent` is based on a high performance lib , `libev`, which is written in C language.

here is what gevent got(from gevent documentation):

* Fast event loop based on `libev` (__epoll on Linux, kqueue on FreeBSD__).
* Lightweight execution units based on `greenlet`.
* API that re-uses concepts from the `Python standard library` (for example there are gevent.event.Events and gevent.queue.Queues).
* Cooperative sockets with SSL support
* DNS queries performed through threadpool or c-ares.
* Monkey patching utility to get 3rd party modules to become cooperative

and all above is included in `Jolla`.

Jolla also got something special:

* Its own simple route rule.
* Easy and fast __json__ response.
* Easy and short program structure for you to develop your project.
* Its easy error handler.

When I wrote `Jolla`,what I want to do is a __high performance API server__ for you to simply obey its short rule,and get your calculation result fast be sent to browser or mobile for your service.so I made `Jolla` as simple as possible.  

##content:

******

* [INSTALL](http://jolla.readthedocs.io/zh/latest/install/)

* [TUTORIAL](http://jolla.readthedocs.io/zh/latest/tutorial/)

if you guys like it.you can click a star on github to support my job.

project address:

[https://github.com/salamer/jolla](https://github.com/salamer/jolla)