# #1422 - How to decouple a response from a request [Closed]

> Username: stuart11n  
> Created at: 2019-01-29 21:31:49 UTC  
> Updated at: 2019-01-29 23:11:34 UTC  
> Closed at: 2019-01-29 23:11:34 UTC  
> Url: https://github.com/boostorg/beast/issues/1422  

How can I decouple a response in a server?   
  
It seems like `http::async_read` shuts down the connection if no response is sent inside the handler? Is that correct?   
  
My problem is I receive a request in my server and read it with `http::async_read`. I need to ship that request off somewhere else for async processing. The response will arrive later, the chain of control is broken so I cannot wait in a lambda etc.  
  
It seems like need to have a custom reader that decouples the request/response apart?

---

## Comment 1

> Username: stuart11n  
> Created at: 2019-01-29 22:58:20 UTC  
> Url: https://github.com/boostorg/beast/issues/1422#issuecomment-458741397  

My current thinking is to handle the request in a thread, and block until the response arrives when I can post it into the `http::async_read` handler, but that doesn't seem very clean.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2019-01-29 22:58:37 UTC  
> Url: https://github.com/boostorg/beast/issues/1422#issuecomment-458741468  

> It seems like http::async_read shuts down the connection if no response is sent inside the handler? Is that correct?  
  
No, it is not correct, but it might seem that way if you are copying the example code (http-server-async for example). If you don't start a new asynchronous operation in the completion handler for the call to **async_read**, then the last `shared_ptr` will go out of scope and the session object will be destroyed.  
  
If you want to "defer" the calculating and sending of the HTTP response then you need to make a copy of the `shared_ptr<session>` and keep it around, and then when you perform the write you put the copy into the handler.  
  
Does this help?

---

## Comment 3

> Username: stuart11n  
> Created at: 2019-01-29 23:02:04 UTC  
> Url: https://github.com/boostorg/beast/issues/1422#issuecomment-458742342  

Of course... thanks! I'll give it a try.
