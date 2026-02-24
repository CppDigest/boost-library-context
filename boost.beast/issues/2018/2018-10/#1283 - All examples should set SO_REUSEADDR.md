# #1283 - All examples should set SO_REUSEADDR [Closed]

> Username: lingtjien  
> Created at: 2018-10-31 10:51:23 UTC  
> Updated at: 2018-11-04 02:00:25 UTC  
> Closed at: 2018-11-04 02:00:25 UTC  
> Url: https://github.com/boostorg/beast/issues/1283  

Steps to reproduce:  
  
1. Download the http_server_async.cpp example from the boos beast website  
2. create a test file called test.txt in the same directory with some text inside  
3. Compile example: `clang++ http_server_async.cpp -std=c++17 -lpthread -lboost_system`  
4. run: `./a.out 0.0.0.0 8000 . 1`  
5. perform get request: `curl -v GET localhost/8000/test.txt`  
6. close the program: `Ctrl+C`  
7. run again: `./a.out 0.0.0.0 8000 . 1`  
  
=> observe that everything works normally as expected  
  
Perform steps 1 till 7 again but replace step 5 with:  
5. perform get request: open a browser (chromium) and type in the URL `localhost/8000/test.txt`  
  
=> observe that the program hangs in the on_accept() method with error code `Invalid argument`  
  
What's different between a curl get request and one performed by the browser that it creates this error and what do I need to change in the example so that it can also handle get requests from the browser properly after a restart of the program?  
  
Edit: http_server_async.cpp not websocket_server_async.cpp example

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-10-31 15:07:13 UTC  
> Url: https://github.com/boostorg/beast/issues/1283#issuecomment-434723475  

websocket-server-async will never handle requests from the browser, because this example only accepts WebSocket Upgrade requests. If you want a server that responds to HTTP GET requests, you need to use either one of theHTTP server examples, or one of the Advanced examples.

---

## Comment 2

> Username: lingtjien  
> Created at: 2018-10-31 15:15:56 UTC  
> Updated at: 2018-10-31 15:18:49 UTC  
> Url: https://github.com/boostorg/beast/issues/1283#issuecomment-434726785  

Oh my apologies, I meant the [http_server_async.cpp](https://www.boost.org/doc/libs/1_66_0/libs/beast/example/http/server/async/http_server_async.cpp) example (I tested the [advanced_server](https://www.boost.org/doc/libs/1_66_0/libs/beast/example/advanced/server/advanced_server.cpp) example shortly too and I observed the same behavior)

---

## Comment 3

> Username: vinniefalco  
> Created at: 2018-10-31 17:23:21 UTC  
> Url: https://github.com/boostorg/beast/issues/1283#issuecomment-434775283  

http-server-async works perfectly for me, I am able to serve entire websites with no trouble at all. I'm not sure why you're having issues, but I suspect the cause is outside of the example.

---

## Comment 4

> Username: lingtjien  
> Created at: 2018-10-31 18:32:00 UTC  
> Url: https://github.com/boostorg/beast/issues/1283#issuecomment-434799043  

Hmm interesting, I seem to observe that when I stop the HTTP server and I've done at least one request from the browser that it somehow still occupies the port or something (if that makes sense?) because the error that I get is the same error that I would get if I tried to start two HTTP servers on the same port.  
  
And if I wait a little while (>10 seconds) between stopping the HTTP server and starting it up again (on the same port) then everything is fine but if I only wait a few seconds between stopping the HTTP server and starting it again I get this error.  
  
It also only seems to occur if I do at least 1 request from the browser. If I don't do any requests or if I do requests by curl I observe no error...  
  
I was hoping that you might have an idea where to look, but otherwise, I'm fine with waiting half a minute between shutting down and starting up the HTTP server too. It's a great library :)

---

## Comment 5

> Username: vinniefalco  
> Created at: 2018-10-31 19:03:57 UTC  
> Url: https://github.com/boostorg/beast/issues/1283#issuecomment-434809579  

> I'm fine with waiting half a minute between shutting down and starting up the HTTP server too. It's a great library :)  
  
Well, mind you that the examples are not part of the library proper, they are just examples. Beast itself does not need or use sockets.  
  
> I was hoping that you might have an idea where to look  
  
Yes, I do! I ran this on Windows which is a bit more relaxed when it comes to reusing the port. It looks like you are on some flavor of Linux so I would investigate the network settings. Are you running as a superuser? Maybe you need to set some kind of reuse-socket option. I thought I was already doing that in the server:  
  
https://github.com/boostorg/beast/blob/f4c3e52a8cabefc97fbe3884b2675a784cf2bed2/example/http/server/async/http_server_async.cpp#L387  
  
You might start with investigating along these lines  
  
Another thing I notice is that this server doesn't have the code to do the clean stop (I left it out for simplicity). So when you say you are stopping the HTTP server I guess you are just killing it. This might be what is causing the operating system to keep a hold of the socket, to make sure the full TCP/IP timeout period elapses (otherwise, late packets could confuse the operating system).  
  
To see if this helps, you can try the advanced server, or copy the code from the advanced server into your http-async example, it is lines 840 through 867 here:  
https://github.com/boostorg/beast/blob/f4c3e52a8cabefc97fbe3884b2675a784cf2bed2/example/advanced/server/advanced_server.cpp#L840  
  
If you run a server that has this code for intercepting SIGINT or SIGTERM, you can shut it down cleanly by pressing CTRL+C or using `kill` to send a SIGINT or SIGTERM.

---

## Comment 6

> Username: lingtjien  
> Created at: 2018-11-01 12:55:14 UTC  
> Updated at: 2018-11-01 12:58:07 UTC  
> Url: https://github.com/boostorg/beast/issues/1283#issuecomment-435032106  

Cool thanks for all the additional info and yes you're correct I am running on Linux and no I'm not running as super user (but I didn't need super user priveleges for the other REST library either and that was build on top of ASIO too).  
  
> Maybe you need to set some kind of reuse-socket option. I thought I was already doing that in the server:  
  
The example from boost.org only has the `acceptor_.open`, `acceptor._bind` and `acceptor_.listen` in the constructor.  
  
Adding the following code as you suggested:  
  
```  
acceptor_.set_option(boost::asio::socket_base::reuse_address(true), ec);  
if(ec)  
{  
  fail(ec, "set_option");  
  return;  
}  
```  
  
works perfectly, so yes adding the reuse option seems to fix it (for me at least).  
  
Maybe you could consider adding this to the examples on boost.org as it isn't that much additional code.  
  
Thank you very much for your help :)

---

## Comment 7

> Username: vinniefalco  
> Created at: 2018-11-01 13:13:13 UTC  
> Url: https://github.com/boostorg/beast/issues/1283#issuecomment-435036755  

Oooh I did not realize that some examples don't set the reuse address option. They should.

---

## Comment 8

> Username: lingtjien  
> Created at: 2018-11-03 11:27:30 UTC  
> Updated at: 2018-11-03 11:28:36 UTC  
> Url: https://github.com/boostorg/beast/issues/1283#issuecomment-435580484  

@vinniefalco I did a quick diff and I noticed two more differences between the example on boost.org and the one you linked too.  
  
1. In the session do_read() you first make the request empty by `req_ = {};` while this code is omitted in the boost.org example.  
2. I noticed that you use a `shared_ptr<std::string>` for the doc_root while the example on boost.org uses a bare `std::string`.  
  
Could these differences potentially lead to problems? (I suppose emptying the request is important as the comment above it states that not doing so leads to undefined behavior?)

---

## Comment 9

> Username: vinniefalco  
> Created at: 2018-11-03 14:11:28 UTC  
> Url: https://github.com/boostorg/beast/issues/1283#issuecomment-435590836  

You linked the version from Boost 1.66 which is out of date and contains bugs (see the release notes)  
https://www.boost.org/doc/libs/1_66_0/libs/beast/example/http/server/async/http_server_async.cpp  
  
Boost 1.68 and later should work correctly. I switched to using the shared pointer  for the document root because it is less likely to be misused through a dangling reference.

---

## Comment 10

> Username: vinniefalco  
> Created at: 2018-11-03 14:13:42 UTC  
> Url: https://github.com/boostorg/beast/issues/1283#issuecomment-435590993  

This is the latest shipping example:  
https://github.com/boostorg/beast/blob/boost-1.68.0/example/http/server/async/http_server_async.cpp  
  
This is the example from the **master** branch  
https://github.com/boostorg/beast/blob/master/example/http/server/async/http_server_async.cpp

---

## Comment 11

> Username: lingtjien  
> Created at: 2018-11-03 17:42:58 UTC  
> Url: https://github.com/boostorg/beast/issues/1283#issuecomment-435606987  

Cool, I didn't notice I was using the example of an older version (I just picked the first thing that showed up in google at the time). Thanks a lot.

---

## Comment 12

> Username: vinniefalco  
> Created at: 2018-11-04 02:00:25 UTC  
> Url: https://github.com/boostorg/beast/issues/1283#issuecomment-435635587  

Hmm...I've reviewed all the examples and it looks like they all have this line  
```  
acceptor_.set_option(boost::asio::socket_base::reuse_address(true), ec);  
```  
I'll go ahead and close this issue since there is nothing actionable in it. If you feel there is still a problem, please do not hesitate to open a new issue, thanks!
