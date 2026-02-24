# #222 - Example HTTP program in the documentation does not compile. [Closed]

> Username: rjahanbakhshi  
> Created at: 2017-01-08 10:40:24 UTC  
> Updated at: 2017-01-08 16:35:24 UTC  
> Closed at: 2017-01-08 16:35:24 UTC  
> Url: https://github.com/boostorg/beast/issues/222  

error: C2039: 'request_v1': is not a member of 'beast::http'  
error: C2039: 'response_v1': is not a member of 'beast::http'

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-01-08 11:57:09 UTC  
> Url: https://github.com/boostorg/beast/issues/222#issuecomment-271146465  

Yes, I see that. Some of the types were renamed to be more streamlined. Here's the source code for the example program, it should compile (its built as part of the continuous integration so I'm sure it works):  
https://github.com/vinniefalco/Beast/blob/master/examples/http_example.cpp  
  
I will update the docs, thanks!

---

## Comment 2

> Username: rjahanbakhshi  
> Created at: 2017-01-08 16:35:24 UTC  
> Url: https://github.com/boostorg/beast/issues/222#issuecomment-271161999  

Hi Vinniefalco,  
Thank you. Yes the one in the example directory compiles and works fine.
