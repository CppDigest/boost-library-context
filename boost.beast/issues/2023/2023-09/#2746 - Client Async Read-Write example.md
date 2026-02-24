# #2746 - Client Async Read/Write example [Closed]

> Username: rajasurampalli  
> Created at: 2023-09-29 09:13:52 UTC  
> Updated at: 2024-01-07 07:49:57 UTC  
> Closed at: 2024-01-07 07:49:57 UTC  
> Url: https://github.com/boostorg/beast/issues/2746  

Hi,  
  
I have used the same code mentioned in the examples https://www.boost.org/doc/libs/1_83_0/libs/beast/example/http/client/async-ssl/http_client_async_ssl.cpp to perform Async Read/Write operation on client side.   
Unfortunately the beast is throwing the error **The requested address is not valid in its context** in on_connect method.   
  
However I'm able to resolve it with  the following code snippet(this is sync code)  
         
       // The io_context is required for all I/O  
        net::io_context ioc;  
  
        // The SSL context is required, and holds certificates  
        ssl::context ssl_context(ssl::context::sslv23_client);  
   
        // These objects perform our I/O  
        tcp::resolver resolver(ioc);  
   
        // Look up the domain name  
        auto const results = resolver.resolve("mywebsite", "https");  
  
### Version of Beast  
  
1.83  
  
### Steps necessary to reproduce the problem  
  
Copy the code available in https://www.boost.org/doc/libs/1_83_0/libs/beast/example/http/client/async-ssl/http_client_async_ssl.cpp and modify according to your website/apis.

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2023-09-29 09:41:55 UTC  
> Url: https://github.com/boostorg/beast/issues/2746#issuecomment-1740604181  

How did you invoke the example code? What website did you look up?

---

## Comment 2

> Username: rajasurampalli  
> Created at: 2023-09-29 09:50:34 UTC  
> Updated at: 2023-09-29 09:53:16 UTC  
> Url: https://github.com/boostorg/beast/issues/2746#issuecomment-1740617804  

its a proprietary website hosted on AWS cloud. I'm able to do synchronous read and write however.   
Wanted to perform an asynchronous read/write.  
example: mywebsite.com, api: api/v1/test.  
The API information passed to "target".  
command line: **host port target** as specified in the example.

---

## Comment 3

> Username: fpelliccioni  
> Created at: 2023-10-23 15:40:53 UTC  
> Url: https://github.com/boostorg/beast/issues/2746#issuecomment-1775485902  

@rajasurampalli   
Could you please provide us with a small code snippet of what's running on the server side? This will allow us to better understand and verify what might be happening.

---

## Comment 4

> Username: rajasurampalli  
> Created at: 2023-10-25 12:52:13 UTC  
> Url: https://github.com/boostorg/beast/issues/2746#issuecomment-1779209424  

I'm sorry I don't have access to the server side code. By the way, I'm able to connect synchronously having an issue during async mode only. So I think it is something to do with the client side.  
FYI, the server side code is written in many languages like GoLang, Python, Java etc.

---

## Comment 5

> Username: ashtum  
> Created at: 2024-01-01 11:24:09 UTC  
> Url: https://github.com/boostorg/beast/issues/2746#issuecomment-1873284840  

@rajasurampalli It seems the issue is that you are unable to connect to the resolved address. You may want to print the resolved address and check its validity. This is not a Boost.Beast or Boost.Asio issue; you may need to investigate further to understand the reasons for encountering this error: https://learn.microsoft.com/en-us/windows/win32/winsock/windows-sockets-error-codes-2?redirectedfrom=MSDN
