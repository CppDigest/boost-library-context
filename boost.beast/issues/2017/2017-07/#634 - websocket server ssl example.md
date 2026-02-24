# #634 - websocket server ssl example [Closed]

> Username: nguoithichkhampha  
> Created at: 2017-07-10 16:31:02 UTC  
> Updated at: 2017-08-08 04:13:41 UTC  
> Closed at: 2017-08-08 04:13:41 UTC  
> Url: https://github.com/boostorg/beast/issues/634  

websocket server ssl would be very useful.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-07-10 16:34:23 UTC  
> Url: https://github.com/boostorg/beast/issues/634#issuecomment-314161532  

https://github.com/vinniefalco/Beast/blob/develop/example/server-framework/wss_ports.hpp#L103  
  
I will also add a more simple example soon

---

## Comment 2

> Username: nguoithichkhampha  
> Created at: 2017-07-11 04:30:51 UTC  
> Updated at: 2017-07-11 04:48:12 UTC  
> Url: https://github.com/boostorg/beast/issues/634#issuecomment-314319940  

thanks for your support. anyway, I have some questions.  
when I should set auto fragment , when should not. I confuse with this option ?  
if set auto fragment it will reduce performance of socket ?

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-07-11 05:11:26 UTC  
> Url: https://github.com/boostorg/beast/issues/634#issuecomment-314324745  

It depends on the value you choose for autofragment. 64K isn't going to reduce performance. 1K might. You want autofragment if your messages are big and you need to be responsive to pings.
