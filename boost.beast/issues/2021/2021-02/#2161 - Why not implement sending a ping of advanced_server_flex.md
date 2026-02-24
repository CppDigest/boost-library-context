# #2161 - Why not implement sending a ping of advanced_server_flex? [Closed]

> Username: dkd1111  
> Created at: 2021-02-10 05:30:06 UTC  
> Updated at: 2021-02-10 06:09:44 UTC  
> Closed at: 2021-02-10 06:09:44 UTC  
> Url: https://github.com/boostorg/beast/issues/2161  

version: boost 1.74.0 / BOOST_BEAST_VERSION 300  
  
- boost 1.67 : https://www.boost.org/doc/libs/1_67_0/libs/beast/example/advanced/server-flex/advanced_server_flex.cpp  
I can check sending a ping code.  
  
-  boost 1.70 : https://www.boost.org/doc/libs/1_70_0/libs/beast/example/advanced/server-flex/advanced_server_flex.cpp  
No implement of sending a ping.  
  
Is there special reasons for disappear of the codes? or just do it myself on version 1.70?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2021-02-10 05:32:13 UTC  
> Url: https://github.com/boostorg/beast/issues/2161#issuecomment-776454315  

The websocket implementation sends pings automatically if you configure it. See:   
https://www.boost.org/doc/libs/1_75_0/libs/beast/doc/html/beast/using_websocket/timeouts.html

---

## Comment 2

> Username: dkd1111  
> Created at: 2021-02-10 06:09:21 UTC  
> Url: https://github.com/boostorg/beast/issues/2161#issuecomment-776469962  

thanks!!
