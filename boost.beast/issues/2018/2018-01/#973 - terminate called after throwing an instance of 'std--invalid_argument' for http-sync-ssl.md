# #973 - terminate called after throwing an instance of 'std::invalid_argument' for http-sync-ssl [Closed]

> Username: erosh  
> Created at: 2018-01-09 16:07:43 UTC  
> Updated at: 2018-01-10 07:55:36 UTC  
> Closed at: 2018-01-10 07:55:36 UTC  
> Url: https://github.com/boostorg/beast/issues/973  

Hi.  
Thank you very much for you great job!  
  
Is it a way to handle such exception?  
```  
terminate called after throwing an instance of 'std::invalid_argument'  
  what():  invalid file descriptor: -1  
```  
  
It can be easily reproduced by running http_client_sync_ssl.cpp  
with wrong(not valid) host value. And this exception unfortunately is not caught.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-01-09 16:12:24 UTC  
> Url: https://github.com/boostorg/beast/issues/973#issuecomment-356331307  

What is the exact command line you are using to invoke the program?

---

## Comment 2

> Username: erosh  
> Created at: 2018-01-10 07:55:36 UTC  
> Url: https://github.com/boostorg/beast/issues/973#issuecomment-356526789  

Hm. Sorry. I figured out that I had another exception in my code during stack unwinding.
