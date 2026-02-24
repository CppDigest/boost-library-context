# #231 - fyi - boost::system can be header-only for beast [Closed]

> Username: larsonmpdx  
> Created at: 2017-01-20 05:34:52 UTC  
> Updated at: 2017-01-30 17:15:17 UTC  
> Closed at: 2017-01-30 17:15:17 UTC  
> Url: https://github.com/boostorg/beast/issues/231  

this trick works (last answer):  
  
http://stackoverflow.com/questions/5089245/boost-asio-as-header-only/40729439#40729439  
  
I can't tell but it might not work for the tests.  Works fine for my application, no need to link to boost::system, the whole thing becomes header-only.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-01-20 12:02:55 UTC  
> Url: https://github.com/boostorg/beast/issues/231#issuecomment-274056794  

Now that is very interesting! I didn't know that!

---

## Comment 2

> Username: vinniefalco  
> Created at: 2017-01-30 17:15:16 UTC  
> Url: https://github.com/boostorg/beast/issues/231#issuecomment-276124498  

Thinking about it, there's nothing really for me to do here, since Beast is header-only. This is good advice for those looking to integrate Beast in their projects, but there's nothing actionable for me so I'm going to go ahead and close this. Thanks!
