# #2228 - Error C2672 'operator __surrogate_func [Closed]

> Username: ghost  
> Created at: 2021-05-11 08:11:33 UTC  
> Updated at: 2021-05-13 01:00:31 UTC  
> Closed at: 2021-05-12 21:38:54 UTC  
> Url: https://github.com/boostorg/beast/issues/2228  



---

## Comment 1

> Username: madmongo1  
> Created at: 2021-05-11 08:31:38 UTC  
> Url: https://github.com/boostorg/beast/issues/2228#issuecomment-838071063  

My first thought is that one of your completion handlers' signatures does not match.  
  
Are you able to isolate which invocation of `bind_front_handler` is causing this?

---

## Comment 2

> Username: ghost  
> Created at: 2021-05-11 08:40:34 UTC  
> Url: https://github.com/boostorg/beast/issues/2228#issuecomment-838083343  

When i look at output compiler I found ``` shared_from_this()```  that cause error C2672, i don t now why

---

## Comment 3

> Username: vinniefalco  
> Created at: 2021-05-11 12:42:55 UTC  
> Url: https://github.com/boostorg/beast/issues/2228#issuecomment-838407601  

What version of VIsual Studio?

---

## Comment 4

> Username: ghost  
> Created at: 2021-05-11 12:54:01 UTC  
> Url: https://github.com/boostorg/beast/issues/2228#issuecomment-838422405  

i work in Visual Studio 2017

---

## Comment 5

> Username: madmongo1  
> Created at: 2021-05-11 15:02:47 UTC  
> Url: https://github.com/boostorg/beast/issues/2228#issuecomment-838638179  

Isn't that quite buggy?

---

## Comment 6

> Username: vinniefalco  
> Created at: 2021-05-11 15:16:30 UTC  
> Url: https://github.com/boostorg/beast/issues/2228#issuecomment-838661403  

Are you using a beta of VS2017 or is it the latest? What is the exact build number?

---

## Comment 7

> Username: ghost  
> Created at: 2021-05-11 16:46:12 UTC  
> Url: https://github.com/boostorg/beast/issues/2228#issuecomment-838812326  

I use Visual Studio professionnal 2017 Version 15.9.35

---

## Comment 8

> Username: ghost  
> Created at: 2021-05-11 16:49:22 UTC  
> Url: https://github.com/boostorg/beast/issues/2228#issuecomment-838816646  

>   
>   
> Isn't that quite buggy?  
  
That error it come from that line   
```  
`  // Receive the HTTP response  
    http::async_read(stream_, buffer_, res_,  
      beast::bind_front_handler(  
        &CRT_Reponse::on_read,  
        shared_from_this()));  
```

---

## Comment 9

> Username: vinniefalco  
> Created at: 2021-05-11 17:01:15 UTC  
> Url: https://github.com/boostorg/beast/issues/2228#issuecomment-838833151  

What happens when you build it in VS2019?

---

## Comment 10

> Username: vinniefalco  
> Created at: 2021-05-11 17:02:34 UTC  
> Url: https://github.com/boostorg/beast/issues/2228#issuecomment-838834975  

Does the information on this page resolve your problem?  
  
https://developercommunity.visualstudio.com/t/error-c2672-operator-surrogate-func-no-matching-ov/571058

---

## Comment 11

> Username: madmongo1  
> Created at: 2021-05-11 19:17:40 UTC  
> Url: https://github.com/boostorg/beast/issues/2228#issuecomment-839031871  

Isn't this the wrong signature?  
```  
  void   
    CRT_Reponse::on_read(  
      std::size_t bytes_transferred)  
  {  
```  
  
shouldn't it take an `error_code` ?

---

## Comment 12

> Username: madmongo1  
> Created at: 2021-05-12 04:07:07 UTC  
> Url: https://github.com/boostorg/beast/issues/2228#issuecomment-839417232  

I think you’ll find that the error message is pointing at that line because it is the line where the entire statement ends.  
  
the problem is that the function signature required by the async_read handler is   
  
void (error_code, size_t)  
  
the function signature resulting from binding the shared pointer to the on_read function is  
  
void (size_t)  
  
so async_read cannot compile because the handler has the wrong signature.
