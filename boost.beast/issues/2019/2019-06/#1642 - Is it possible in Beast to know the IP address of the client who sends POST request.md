# #1642 - Is it possible in Beast to know the IP address of the client who sends POST request? [Closed]

> Username: Rabahjamal  
> Created at: 2019-06-25 14:01:13 UTC  
> Updated at: 2019-07-25 14:52:24 UTC  
> Closed at: 2019-07-25 14:52:24 UTC  
> Url: https://github.com/boostorg/beast/issues/1642  

I'm using Beast to implement a RESTful web service, but I want my service to accept POST requests from specific IP only as a kind of authentication. So, does Beast provide a way to know the IP address of the client? Thanks in advance.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-06-25 14:01:47 UTC  
> Updated at: 2019-06-25 14:02:11 UTC  
> Url: https://github.com/boostorg/beast/issues/1642#issuecomment-505458847  

Beast doesn't know about sockets, that's part of Asio. And yes, Asio sockets can report the IP address of both the remote and local hosts. See:  
https://www.boost.org/doc/libs/1_70_0/doc/html/boost_asio/reference/basic_socket/remote_endpoint.html

---

## Comment 2

> Username: djarek  
> Created at: 2019-06-25 14:08:47 UTC  
> Url: https://github.com/boostorg/beast/issues/1642#issuecomment-505461780  

Note that IP addresses can be spoofed, so this kind of authentication isn't very useful and is, in fact, about as secure as sending passwords over in plain-text.

---

## Comment 3

> Username: stale[bot]  
> Created at: 2019-07-25 14:12:28 UTC  
> Url: https://github.com/boostorg/beast/issues/1642#issuecomment-515059992  

This issue has been open for a while with no activity, has it been resolved?
