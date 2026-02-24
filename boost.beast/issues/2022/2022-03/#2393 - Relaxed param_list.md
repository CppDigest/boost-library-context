# #2393 - Relaxed param_list [Closed]

> Username: gummif  
> Created at: 2022-03-03 10:50:48 UTC  
> Updated at: 2022-06-14 17:37:02 UTC  
> Closed at: 2022-06-14 17:37:02 UTC  
> Url: https://github.com/boostorg/beast/issues/2393  

I would like to bring up to relax the requirements for `param_list` (or make it optional by providing an argument to the constructor e.g.) to not require a leading semicolon as that it standard practice. See https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Disposition where these examples are given:  
```  
Content-Type: text/html; charset=utf-8  
Content-Type: multipart/form-data;boundary="boundary"  
Content-Disposition: attachment; filename="filename.jpg"  
```  
All these fail currently and would need to be manually parsed, or one would need to prepend a ; before the string before passing to `param_list`.  
  
Is this reasonable?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-06-14 17:37:02 UTC  
> Url: https://github.com/boostorg/beast/issues/2393#issuecomment-1155501202  

Perhaps for now you could just make a copy of the code into your own project and adjust it to taste? It isn't that much code. I'm working on a new library that will handle this much better.
