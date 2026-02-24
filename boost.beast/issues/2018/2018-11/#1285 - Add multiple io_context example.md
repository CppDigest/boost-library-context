# #1285 - Add multiple io_context example [Closed]

> Username: vinniefalco  
> Created at: 2018-11-01 12:29:20 UTC  
> Updated at: 2024-02-14 09:28:25 UTC  
> Closed at: 2024-02-14 09:28:25 UTC  
> Url: https://github.com/boostorg/beast/issues/1285  

We could use a third advanced server that has multiple threads, one io_context per thread.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-02-23 03:41:58 UTC  
> Url: https://github.com/boostorg/beast/issues/1285#issuecomment-466612876  

This can be done with a custom executor now

---

## Comment 2

> Username: benstadin  
> Created at: 2019-12-15 01:48:34 UTC  
> Url: https://github.com/boostorg/beast/issues/1285#issuecomment-565769425  

I just found this after trying your example at [1] with some quick and dirty hacks to get it compiling with the current version of Beast. However, it crashes after a few requests. My guess is it's related to reusing the context, but it's not clear to me how to reset or guard the context correctly.   
  
I've created a gist here using the advanced server example with hacks to the context pool:  
https://gist.github.com/benstadin/4898c4a57bc55cc4ba2d483104335890  
  
The crash I see after a few requests is like:  
`Assertion failed: (value > 0 && value < 100), function version, file /usr/local/include/boost/beast/http/message.hpp, line 342.`  
  
I'm looking to improve performance with multiple CPU cores, and looking at cpu time spent in the profiler looks like this could help significantly. At the moment requests / second settle near 60k (not bad at all though), using 8 or 16 threads has only minor effect on my 8 core i9 CPU (like from 54k to 60k rps).   
  
[1] https://github.com/vinniefalco/beast/blob/multi-io/example/advanced/server-multi-io-context/advanced_server_multi_io_context.cpp

---

## Comment 3

> Username: benstadin  
> Created at: 2019-12-15 11:19:11 UTC  
> Updated at: 2019-12-15 11:19:38 UTC  
> Url: https://github.com/boostorg/beast/issues/1285#issuecomment-565800071  

Edit: I've updated a gist. It doesn't crash anymore (issue was behind the monitor).   
  
But would you consider this approach a good design in general, or should this rather be done with a custom executor now?   
  
Some measurements show roughly a 50% improvement in requests/s when using multiple io contexts (running on OS X 10.15.2, 8-core Intel i9):  
  
1) Using advanced server example (changed to return immediately a string "Test" to avoid disk io).  
  
```  
wrk -t8 -c100 -d10s -H 'Connection: keep-alive' --latency --timeout 60 http://127.0.0.1:8080  
Running 10s test @ http://127.0.0.1:8080  
  8 threads and 100 connections  
  Thread Stats   Avg      Stdev     Max   +/- Stdev  
    Latency     1.52ms  247.35us   3.77ms   71.85%  
    Req/Sec     7.88k   661.47     9.67k    62.62%  
  Latency Distribution  
     50%    1.47ms  
     75%    1.63ms  
     90%    1.90ms  
     99%    2.24ms  
  633900 requests in 10.10s, 68.31MB read  
Requests/sec:  62759.35  
Transfer/sec:      6.76MB  
```  
  
2) Using multiple io contexts  
  
```  
wrk -t8 -c100 -d10s -H 'Connection: keep-alive' --latency --timeout 60 http://127.0.0.1:8080  
Running 10s test @ http://127.0.0.1:8080  
  8 threads and 100 connections  
  Thread Stats   Avg      Stdev     Max   +/- Stdev  
    Latency     0.98ms   38.57us   3.23ms   97.16%  
    Req/Sec    12.21k   194.38    12.47k    83.54%  
  Latency Distribution  
     50%    0.98ms  
     75%    0.99ms  
     90%    1.00ms  
     99%    1.08ms  
  981676 requests in 10.10s, 252.77MB read  
Requests/sec:  97192.14  
Transfer/sec:     25.03MB  
```

---

## Comment 4

> Username: vinniefalco  
> Created at: 2019-12-15 13:17:30 UTC  
> Url: https://github.com/boostorg/beast/issues/1285#issuecomment-565808877  

Nice numbers!! But this `bump_context` is a bit of an ugly duckling. It seems to work though, so there's that. I have not thought about it deeply yet so I don't have a better answer for you.

---

## Comment 5

> Username: benstadin  
> Created at: 2019-12-18 23:40:55 UTC  
> Url: https://github.com/boostorg/beast/issues/1285#issuecomment-567262025  

That's ok. Though I'm giving up on multi IO support for now. I found some unreliable behavior in my application which I couldn't get on the bottom of it. E.g. when stress testing and doing manual REST calls all is fine, but in some of my test cases the acceptor just doesn't trigger on incoming requests.   
I'd like to give it another shot at some point in the near future when I understand more about custom executors.

---

## Comment 6

> Username: vinniefalco  
> Created at: 2019-12-19 03:32:37 UTC  
> Url: https://github.com/boostorg/beast/issues/1285#issuecomment-567316174  

Hmmm the way to do multi io-context for a server, is to have a separate listening socket for each `io_context`. You will have to use SO_REUSEPORT (or something like that) in order that you can have multiple sockets bound to the same port.

---

## Comment 7

> Username: benstadin  
> Created at: 2019-12-19 04:07:33 UTC  
> Updated at: 2019-12-19 04:09:12 UTC  
> Url: https://github.com/boostorg/beast/issues/1285#issuecomment-567323128  

Ah, ok. I only see this in the example at [1] and have that also in my project:  
  
`acceptor_.set_option(net::socket_base::reuse_address(true), ec);`  
  
There is only one listener in the example, and there is a context "round-robined" from the store after accept for each new session.   
  
I think there there might also be another problem when using multiple listeners in case a listener accepts a connection, bumps a context for the new socket, and afterwards another listener bumps a context already used by a listener/acceptor.  
  
[1] https://github.com/vinniefalco/beast/blob/multi-io/example/advanced/server-multi-io-context/advanced_server_multi_io_context.cpp

---

## Comment 8

> Username: vinniefalco  
> Created at: 2019-12-19 04:09:32 UTC  
> Url: https://github.com/boostorg/beast/issues/1285#issuecomment-567323437  

That advanced-server-multi-io-context code does not look thread safe!!! It uses a non-atomic counter? How can that possibly work right.

---

## Comment 9

> Username: klemens-morgenstern  
> Created at: 2022-09-24 06:10:03 UTC  
> Url: https://github.com/boostorg/beast/issues/1285#issuecomment-1256876667  

Does that mean an `asio::thread_pool example now?

---

## Comment 10

> Username: vinniefalco  
> Created at: 2022-09-25 00:24:51 UTC  
> Url: https://github.com/boostorg/beast/issues/1285#issuecomment-1257089705  

At this point, I don't care too much about proliferation of examples. A thread-pool example should be a general asio technique/example/skill, it has little to do with Beast.
