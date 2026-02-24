# #184 - Server application won't bind to ports on IP address other than my internal ones [Closed]

> Username: DragonOsman  
> Created at: 2018-12-30 23:36:03 UTC  
> Updated at: 2020-12-30 00:58:31 UTC  
> Closed at: 2020-12-30 00:58:30 UTC  
> Url: https://github.com/boostorg/asio/issues/184  

As the title says, my server application doesn't bind to any ports on any IP address other than my computer's internal IP addresses (the localhost and 192.168.x.x ones).  The application is the one whose source code is on GitHub [here](https://github.com/DragonOsman/currency_converter/).  
  
I used the asynchronous server and client examples from the Boost.Beast GitHub repository for the networking code (this link for the server: [link](https://github.com/boostorg/beast/blob/develop/example/http/server/async/http_server_async.cpp); this link for the client: [link](https://github.com/boostorg/beast/blob/develop/example/http/client/async/http_client_async.cpp)).  But when I run it using my external IP address, I get the error documented on lines 471 and 472:  
```cpp  
// Bind to the server address  
acceptor_.bind(endpoint, ec);  
if (ec)  
{  
    std::cerr << "Lines 471 and 472:\n";  
    fail(ec, "bind");  
    return;  
}  
```  
with the error itself being:  
```  
bind: The requested address is not valid in its context  
```  
And I've heard from whoever has tried to download and then run the server application that it just closes on its own after a while.  I also tried to deploy it to Heroku as a Docker container before and found that it wouldn't bind to any ports they tried to bind it to and they had to kill it with SIGKILL.    
  
I tried to look for the code for `bind` in Asio, but I don't really know where to look.  Some help on that would also be appreciated.  I might be able to figure it from reading that code.  If I can't, I'll come back here.

---

## Comment 1

> Username: DragonOsman  
> Created at: 2019-01-06 19:05:27 UTC  
> Url: https://github.com/boostorg/asio/issues/184#issuecomment-451765897  

Could someone please help me out?  Thanks in advance.

---

## Comment 2

> Username: ghost  
> Created at: 2020-12-30 00:58:29 UTC  
> Url: https://github.com/boostorg/asio/issues/184#issuecomment-752290892  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#661](https://github.com/chriskohlhoff/asio/issues/661).
