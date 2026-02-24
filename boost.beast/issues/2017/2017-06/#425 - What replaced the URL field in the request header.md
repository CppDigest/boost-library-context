# #425 - What replaced the URL field in the request header? [Closed]

> Username: bitbugprime  
> Created at: 2017-06-05 22:14:34 UTC  
> Updated at: 2017-06-29 11:49:17 UTC  
> Closed at: 2017-06-06 01:47:08 UTC  
> Url: https://github.com/boostorg/beast/issues/425  

I used to set the url field (a simple std::string) in the request header object to make http requests. Now that field has disappeared. How do I set the URL for the request now, and should it have the same value that it had before this change?

---

## Comment 1

> Username: Hardy5012  
> Created at: 2017-06-29 10:46:32 UTC  
> Updated at: 2017-06-29 10:48:22 UTC  
> Url: https://github.com/boostorg/beast/issues/425#issuecomment-311930585  

@randian  How do you set the URL for the request now?

---

## Comment 2

> Username: vinniefalco  
> Created at: 2017-06-29 11:49:17 UTC  
> Url: https://github.com/boostorg/beast/issues/425#issuecomment-311943432  

```  
req.target("/url");  
```
