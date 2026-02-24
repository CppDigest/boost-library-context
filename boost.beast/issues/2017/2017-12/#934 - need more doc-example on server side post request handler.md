# #934 - need more doc/example on server side post request handler [Closed]

> Username: rinick  
> Created at: 2017-12-10 09:11:56 UTC  
> Updated at: 2017-12-11 01:57:07 UTC  
> Closed at: 2017-12-11 01:53:18 UTC  
> Url: https://github.com/boostorg/beast/issues/934  

almost all the examples accept only get and head method.  
it's very easy to build a webserver for http get or ws quickly just from the examples.  
but building a server to handle rest api or uploading files is a different story. current doc and examples are not very helpful.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-12-10 14:39:16 UTC  
> Updated at: 2017-12-10 14:39:36 UTC  
> Url: https://github.com/boostorg/beast/issues/934#issuecomment-350552781  

The documentation and examples are not intended to show how to build a particular server step by step, they only serve to cover the basics. There are a limitless number of ways that a REST server or file upload may be implemented. Beast is a low-level library, it is up to the user to decide how they want to do things, it is not the role of the examples and documentation to provide guidance on all possible use-cases.  
  
However, I am certainly more than happy to help here if you have specific questions. Is there something I can answer for you?

---

## Comment 2

> Username: rinick  
> Created at: 2017-12-10 17:58:56 UTC  
> Url: https://github.com/boostorg/beast/issues/934#issuecomment-350566139  

Thanks for your reply.  
when I got a request.body(), it's not very clear to me how should I use the multi-buffer  
also if the post data is not completed in the first update, what's the correct way to read the remain buffers to come?

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-12-10 19:39:48 UTC  
> Url: https://github.com/boostorg/beast/issues/934#issuecomment-350575740  

>when I got a request.body(), it's not very clear to me how should I use the multi-buffer  
  
The multi-buffer is a **DynamicBuffer** which is described here:  
  
http://www.boost.org/doc/libs/master/libs/beast/doc/html/beast/concepts/DynamicBuffer.html  
  
If you are unfamiliar with Asio's **ConstBufferSequence** and **MutableBufferSequence** concepts, you might be more comfortable working with the message body as a `std::string`. You can do that by using the `beast::http::string_body`:  
  
```  
beast::http::request<beast::http::string_body> req;  
beast::http::response<beast::http::string_body> res;  
```  
  
The return type of `req.body()` and `res.body()` will be `std::string` in this case.  
  
> if the post data is not completed in the first update, what's the correct way to read the remain buffers to come?  
  
That depends on which overload of `beast::http::read` you are using. There are several versions. This one will block until the entire message is read in (there will be nothing remaining):  
  
http://www.boost.org/doc/libs/master/libs/beast/doc/html/beast/ref/boost__beast__http__read/overload3.html  
  
If you are using `beast::http::read_some`, the function `parser::is_done()` will return `true` when the message is complete.  
  
Consider reading the HTTP section of the documentation, where this is explained:  
  
http://www.boost.org/doc/libs/master/libs/beast/doc/html/beast/using_http.html  
  
Hope this helps!

---

## Comment 4

> Username: rinick  
> Created at: 2017-12-11 01:53:18 UTC  
> Url: https://github.com/boostorg/beast/issues/934#issuecomment-350603347  

This really helps, just by switching to string_body, I'm already able to continue my work.   
  
but eventually I will still switch to DynamicBuffer, and I will probably need read overload2 since I need different end pointer to have different behavior.  
  
Thank you for all these explanation.

---

## Comment 5

> Username: rinick  
> Created at: 2017-12-11 01:57:06 UTC  
> Url: https://github.com/boostorg/beast/issues/934#issuecomment-350603760  

btw, I still think it won't hurt to have a example, so other user won't need to search in issues to find these answer :p
