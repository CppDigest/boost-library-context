# #1696 - Implementing a fair executor for low latency applications [Closed]

> Username: benstadin  
> Created at: 2019-09-08 01:27:27 UTC  
> Updated at: 2019-10-09 16:47:41 UTC  
> Closed at: 2019-10-09 16:47:41 UTC  
> Url: https://github.com/boostorg/beast/issues/1696  

Is it possible to intercept / add a custom executor to pending async HTTP(S) reads/writes in order to manage who is next in line?   
  
I get good benchmark results using Beast, but I want to improve on deviation and maximum latency (e.g. in my stress test I get about 8ms average and 225ms max).

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-09-08 02:13:20 UTC  
> Url: https://github.com/boostorg/beast/issues/1696#issuecomment-529162610  

Hmmm.... interesting idea. For HTTP/S you need to make sure that you're using the `beast::ssl_stream`, as it works around the problem that `asio::ssl::stream` does not fully support scatter-gather I/O (which cause extra kernel transitions and increase latency).  
  
For ordering, I assume you mean controlling "next in line" across multiple connections (and not trying to issue multiple reads for the same connection)? Yes I believe a custom executor can allow this, but you will have to specify the executor upon construction of the stream (preferred) or upon the call to the initiating function.  
  
The easiest way to do it is to specify a custom executor in the `beast::basic_stream` as the class template parameter, and provide an instance of the executor in the constructor:  
https://www.boost.org/doc/libs/1_71_0/libs/beast/doc/html/beast/ref/boost__beast__basic_stream.html  
  
Asio also supports customizing the executor on the socket itself, in the same fashion so you can do that if you'd like. Alternatively you can use the polymorphic executor and not worry about template parameter types (at the cost of a bit of type-erasure).

---

## Comment 2

> Username: vinniefalco  
> Created at: 2019-09-08 02:14:51 UTC  
> Url: https://github.com/boostorg/beast/issues/1696#issuecomment-529162672  

If you desire the lowest possible latency, these Stack Overflow answers might be enlightening:  
https://stackoverflow.com/questions/44446984/can-boost-asio-be-used-to-build-low-latency-applications

---

## Comment 3

> Username: vinniefalco  
> Created at: 2019-09-09 16:36:12 UTC  
> Url: https://github.com/boostorg/beast/issues/1696#issuecomment-529563012  

I'm interested in assisting you with this, as custom executors have become a topic of interest in WG21. We can discuss it here or if you like you can email me (my address is in my GitHub profile).

---

## Comment 4

> Username: stale[bot]  
> Created at: 2019-10-09 16:39:00 UTC  
> Url: https://github.com/boostorg/beast/issues/1696#issuecomment-540084082  

This issue has been open for a while with no activity, has it been resolved?
