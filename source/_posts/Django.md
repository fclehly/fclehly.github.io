---
title: Django
date: 2019-09-28 20:21:45
tags:
- django
categories:
- note
---

# WSGI
Web Server Gateway Interface。这是python中定义的一个网关协议，规定了app如何与webserver交互。WSGI是python中定义的一个网关协议规定web server如何跟web应用程序交互。 这个协议最主要的目的就是保证Python中所有web server程序或者gateway程序能够通过统一的协议跟web框架或者说web应用程序进行交互，这对于部署web程序来说很重要，你可以选择任何一个实现了WSGI协议的web server跑你的程序。

如果没有这个协议，那么每个程序每个web server可能都会实现各自的接口，最终的结果是一团乱。使用统一协议的另一个好处是web应用框架只需要实现wsgI就可以跟外部请求进行交互了，不用去针对某个web server来独立开发，交互逻辑开发者可以把精力放在框架本身。

wsgi分为两部分，其中一部分是web server或者gateway它监听某个端口上接受外部的请求，另一部分是web application，那不是我接受请求之后会通过wsgi协议规定的方式把数据传递给web application。Web application中处理完之后，设置对应的状态和header之后返回body部分，web server拿到返回数据之后，再进行HTTP协议的封装，最终返回完整的HTTP-Response数据。