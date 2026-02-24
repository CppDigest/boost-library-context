# #1212 - writing asynchronously generated body data [Closed]

> Username: rhashimoto  
> Created at: 2018-07-30 20:25:54 UTC  
> Updated at: 2018-07-30 22:22:58 UTC  
> Closed at: 2018-07-30 22:15:31 UTC  
> Url: https://github.com/boostorg/beast/issues/1212  

What is the best way to output asynchronously generated body data? As an example, let's say I'm writing an HTTP proxy server. When an HTTP request comes in, my server makes an upstream request and reads the upstream response asynchronously. How should I forward that asynchronous response data to the client?  
  
From my reading of the docs, it looks like I should implement a custom Body and associated BodyWriter. My understanding is I would need to block in the BodyWriter get() method until some upstream data is available to return. That seems like a non-ASIO thing to do so I'm wondering if I'm missing an asynchronous way to do this.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-07-30 21:15:15 UTC  
> Url: https://github.com/boostorg/beast/issues/1212#issuecomment-409012960  

> I'm wondering if I'm missing an asynchronous way to do this.  
  
Yes, you are. You should never attempt to call initiating functions from within the implementation of any BodyReader or BodyWriter. The documentation provides an example on how to write a proxy:  
https://www.boost.org/doc/libs/1_67_0/libs/beast/doc/html/beast/more_examples/http_relay.html  
  
If you have questions about this code I would be more than happy to answer them.

---

## Comment 2

> Username: rhashimoto  
> Created at: 2018-07-30 22:15:31 UTC  
> Url: https://github.com/boostorg/beast/issues/1212#issuecomment-409029669  

Cool, thanks for the quick response! I get it now; I wasn't understanding buffer_body before.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2018-07-30 22:22:58 UTC  
> Url: https://github.com/boostorg/beast/issues/1212#issuecomment-409031344  

Please let me know how `buffer_body` works out for you. I'm not entirely happy with it.
