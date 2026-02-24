# #1025 - std::function< response(request) > based on send_lambda from http_server_coro.cpp [Closed]

> Username: jdmairs  
> Created at: 2018-02-16 21:51:17 UTC  
> Updated at: 2018-02-27 19:40:28 UTC  
> Closed at: 2018-02-27 19:40:16 UTC  
> Url: https://github.com/boostorg/beast/issues/1025  

It appears that the send_lambda that passed into handle_request can accept any response.  Meaning a response with string_body or empty_body or file_body etc.    
  
I am trying to store a std::functional< beast::response< any body > ( beast::request<http::string_body>)>  
  
I'm calling this std::functional my "request handler".  I'm storing a bunch of them in a tuple and calling it my router.  All this is motivated/inspired from the very fine cppcon2017 "Using Functional Programming Patterns to build a clean and simple HTTP routing API"  
  
Any help or examples on how to write a function, functor, lambda etc that can return "any type of body_type"  response would be appreciated.    
  
If I should have sent this request through boost mailing list I apologize.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-02-16 21:58:17 UTC  
> Url: https://github.com/boostorg/beast/issues/1025#issuecomment-366370313  

>If I should have sent this request through boost mailing list I apologize.  
  
GitHub issues is fine, thanks for asking!  
  
>Any help or examples on how to write a function, functor, lambda etc that can return "any type of body_type" response would be appreciated.  
  
Phew, that's a tall order. I guess you need to type-erase the **Body**. You would need to implement your own **Body** type which stores a type-erased reference to the original body. And you would need to implement a type-erased **BodyWriter** for it. These concepts are well specified:  
  
http://www.boost.org/doc/libs/1_66_0/libs/beast/doc/html/beast/concepts/Body.html  
http://www.boost.org/doc/libs/1_66_0/libs/beast/doc/html/beast/concepts/BodyWriter.html  
  
There is also my CppCon 2017 presentation which will help you understand these concepts:  
  
https://www.youtube.com/watch?v=WsUnnYEKPnI  
  
Where you will have a bit of trouble is the type-erasing of the **ConstBufferSequence** which the **BodyWriter** returns to the stream algorithm. You'll have to allocate memory for that. In fact you'll have to allocate a few pieces of memory to fully type-erase the body.  
  
I can help if you have specific questions.

---

## Comment 2

> Username: jdmairs  
> Created at: 2018-02-16 22:01:40 UTC  
> Url: https://github.com/boostorg/beast/issues/1025#issuecomment-366371119  

Thanks for the quick response.  I did watch Making Classes great again...I've been mumbling down the hallway, "All problems can be solved by another level of indirection."  
  
Since I'm not sure what type-erase means nor ever done one I'm starting to feel this ship listing to port.    
  
John

---

## Comment 3

> Username: vinniefalco  
> Created at: 2018-02-16 22:03:40 UTC  
> Updated at: 2018-02-16 22:04:28 UTC  
> Url: https://github.com/boostorg/beast/issues/1025#issuecomment-366371562  

Well `std::function` represents a type-erased function. You construct it with a particular type and yet the `std::function` template parameter list does not name that type. Because it is erased :) That help?  
  
Try this blog post:  
https://akrzemi1.wordpress.com/2013/11/18/type-erasure-part-i/

---

## Comment 4

> Username: jdmairs  
> Created at: 2018-02-16 22:14:18 UTC  
> Url: https://github.com/boostorg/beast/issues/1025#issuecomment-366373941  

maybe there is a baby step pragmatic way where I return a tuple or variant of "bodies" until I understand type erasure better.

---

## Comment 5

> Username: jdmairs  
> Created at: 2018-02-16 22:20:46 UTC  
> Url: https://github.com/boostorg/beast/issues/1025#issuecomment-366375247  

I realized in the end...all my responses are string responses.  Even the file_body turns into a bunch of bytes which can be stored in string_body.  
  
Thanks again and hope to hear you on cppcast again.

---

## Comment 6

> Username: vinniefalco  
> Created at: 2018-02-17 00:22:22 UTC  
> Url: https://github.com/boostorg/beast/issues/1025#issuecomment-366396252  

Well, `file_body` is optimized on Windows to use `::TransmitFile` which avoids transitions to and from the kernel from user-space so you will lose that with `string_body`. But you will also lose it if you type-erase the body.

---

## Comment 7

> Username: zlojvavan  
> Created at: 2018-02-19 07:34:58 UTC  
> Url: https://github.com/boostorg/beast/issues/1025#issuecomment-366609017  

what about ::TransmitPackets?

---

## Comment 8

> Username: vinniefalco  
> Created at: 2018-02-19 13:17:54 UTC  
> Updated at: 2018-02-19 13:18:02 UTC  
> Url: https://github.com/boostorg/beast/issues/1025#issuecomment-366690595  

>what about ::TransmitPackets?  
  
Interesting, but beast uses `::TransmitFile`

---

## Comment 9

> Username: zlojvavan  
> Created at: 2018-02-20 09:11:35 UTC  
> Url: https://github.com/boostorg/beast/issues/1025#issuecomment-366914206  

I know it does for sending files while TransmitPackets is intended for optimized sending of memory buffers

---

## Comment 10

> Username: vinniefalco  
> Created at: 2018-02-27 16:38:33 UTC  
> Url: https://github.com/boostorg/beast/issues/1025#issuecomment-368941495  

Is there anything actionable in this issue or is it resolved?

---

## Comment 11

> Username: jdmairs  
> Created at: 2018-02-27 19:25:04 UTC  
> Url: https://github.com/boostorg/beast/issues/1025#issuecomment-368995901  

I cannot access github from work right now but it can be closed.  
  
Thanks.  
  
  
On Tue, Feb 27, 2018 at 11:38 AM, Vinnie Falco <notifications@github.com>  
wrote:  
  
> Is there anything actionable in this issue or is it resolved?  
>  
> —  
> You are receiving this because you authored the thread.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/beast/issues/1025#issuecomment-368941495>,  
> or mute the thread  
> <https://github.com/notifications/unsubscribe-auth/AFXT6HZRaCBV-BFf4CV2YBgQS6CzKAoCks5tZC-MgaJpZM4SI-UH>  
> .  
>

---

## Comment 12

> Username: vinniefalco  
> Created at: 2018-02-27 19:40:16 UTC  
> Updated at: 2018-02-27 19:40:28 UTC  
> Url: https://github.com/boostorg/beast/issues/1025#issuecomment-369000730  

Thanks, I'll go ahead and close this. Feel free to open new issues as needed.
