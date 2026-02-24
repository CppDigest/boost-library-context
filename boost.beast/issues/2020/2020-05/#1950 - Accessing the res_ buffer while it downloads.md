# #1950 - [http_client_async_ssl] Accessing the res_ buffer while it downloads [Closed]

> Username: FabrizioDys  
> Created at: 2020-05-11 10:13:23 UTC  
> Updated at: 2020-06-05 13:10:05 UTC  
> Closed at: 2020-06-05 13:10:05 UTC  
> Url: https://github.com/boostorg/beast/issues/1950  

Good morning, I am currently using the example [HTTP CLIENT ASYNC SSL](https://www.boost.org/doc/libs/develop/libs/beast/example/http/client/async-ssl/http_client_async_ssl.cpp) to download a file from a https site and everything works smoothly. Now I need to access the ` res_` that contains the response while the response is still downloading. For example, I am downloading a very large file and while it downloads I need to access to the part that has already been downloaded and send it to another program ( this has to be done several times during the download ). Here's my questions:  
  
-Where can I access res_ using the code I linked above while it downloads?  
-Is there a way to see how much bytes have been downloaded up to a specific moment?  
-I made a test with this Async client by simply printing to screen a message right after the ioc.run(), and I was expecting that this message would be printed before the actual stream downloaded via HTTP, but it's displayed right after, why?  
  
Thank you for your help and for Boost, that is being really useful for my project.

---

## Comment 1

> Username: madmongo1  
> Created at: 2020-05-11 11:25:02 UTC  
> Url: https://github.com/boostorg/beast/issues/1950#issuecomment-626643474  

Hi @FabrizioDys ,  
  
Have you seen the documentation for http::async_read_some?  
  
This gives you direct access to the parser which contains the respose.  
  
https://www.boost.org/doc/libs/1_73_0/libs/beast/doc/html/beast/ref/boost__beast__http__async_read_some.html  
  
Let me know if you need more help, or join the user/developer community  on cpplang slack inthe #beast channel:  
  
https://cppalliance.org/slack/

---

## Comment 2

> Username: vinniefalco  
> Created at: 2020-05-11 11:51:58 UTC  
> Url: https://github.com/boostorg/beast/issues/1950#issuecomment-626655013  

> -I made a test with this Async client by simply printing to screen a message right after the ioc.run(), and I was expecting that this message would be printed before the actual stream downloaded via HTTP, but it's displayed right after, why?  
  
You're supposed to already understand how Asio works. See:   
https://www.boost.org/doc/libs/1_73_0/doc/html/boost_asio/reference/io_context/run/overload1.html

---

## Comment 3

> Username: madmongo1  
> Created at: 2020-06-05 13:10:05 UTC  
> Url: https://github.com/boostorg/beast/issues/1950#issuecomment-639470205  

Closing for lack of activity.   
If you have any more questions, please don't hesitate to get in touch.
