# #719 - websocket_client_ssl example:  prints "Hello world!a" [Closed]

> Username: Ivers87  
> Created at: 2017-08-06 13:09:27 UTC  
> Updated at: 2017-08-06 19:45:28 UTC  
> Closed at: 2017-08-06 18:52:21 UTC  
> Url: https://github.com/boostorg/beast/issues/719  

I've built your example websocket_client_ssl.cpp.   
  
I expect exactly the same string, as i sent to "echo.websocket.org", but it returns 'a'  
in the end of my string.   
  
Am i doing something wrong? Thank you!

---

## Comment 1

> Username: Ivers87  
> Created at: 2017-08-06 13:48:03 UTC  
> Url: https://github.com/boostorg/beast/issues/719#issuecomment-320508067  

Oh, it should be   
ws.write(boost::asio::buffer(std::string("Hello, world!")), ec);  
  
That way it works well.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2017-08-06 16:51:16 UTC  
> Url: https://github.com/boostorg/beast/issues/719#issuecomment-320518414  

Yep, `boost::asio::buffer` will treat string literals as an array so it will include the null terminator, which is not what you want. Is there anything else I can help with or can we close the issue? Thanks!

---

## Comment 3

> Username: Ivers87  
> Created at: 2017-08-06 18:52:18 UTC  
> Url: https://github.com/boostorg/beast/issues/719#issuecomment-320525045  

Yeah, we can close the issue. Congraduation with entry into Boost.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2017-08-06 19:45:28 UTC  
> Url: https://github.com/boostorg/beast/issues/719#issuecomment-320528019  

Thank you very much!!
