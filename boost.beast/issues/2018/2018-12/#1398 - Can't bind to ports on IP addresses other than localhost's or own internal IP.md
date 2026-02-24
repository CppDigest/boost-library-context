# #1398 - Can't bind to ports on IP addresses other than localhost's or own internal IP [Closed]

> Username: DragonOsman  
> Created at: 2018-12-30 23:22:25 UTC  
> Updated at: 2019-01-13 01:45:13 UTC  
> Closed at: 2019-01-13 01:45:13 UTC  
> Url: https://github.com/boostorg/beast/issues/1398  

As the title says, my server application doesn't bind to any ports on any IP address other than my computer's internal IP addresses (the localhost and 192.168.x.x ones).  The application is the one whose source code is on GitHub [here](https://github.com/DragonOsman/currency_converter/).  
  
I used the asynchronous server and client examples from this repository for the networking code.  But when I run it using my external IP address, I get the error documented on lines 471 and 472:  
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
  
I tried to look for the code for `bind` in Asio, but I don't really know where to look.  Some help on that would also be appreciated.  I might be figure it from reading that code.  If I can't, I'll come back here.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-12-30 23:27:13 UTC  
> Url: https://github.com/boostorg/beast/issues/1398#issuecomment-450593965  

Port numbers below 1,000 are reserved for system services and require elevated permissions. Anyway this is not a Beast problem.

---

## Comment 2

> Username: DragonOsman  
> Created at: 2018-12-30 23:29:57 UTC  
> Url: https://github.com/boostorg/beast/issues/1398#issuecomment-450594115  

I was trying 8443 actually.    
  
So I should ask on Boost.Asio's GitHub repo, then?

---

## Comment 3

> Username: vinniefalco  
> Created at: 2018-12-30 23:33:54 UTC  
> Url: https://github.com/boostorg/beast/issues/1398#issuecomment-450594278  

You could ask there but this looks like a problem either in your code or with your system configuration.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2019-01-13 01:45:13 UTC  
> Url: https://github.com/boostorg/beast/issues/1398#issuecomment-453794989  

Not a Beast problem.
