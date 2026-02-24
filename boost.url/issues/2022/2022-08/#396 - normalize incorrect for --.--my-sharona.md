# #396 - normalize incorrect for "/.//my:sharona" [Closed]

> Username: vinniefalco  
> Created at: 2022-08-12 22:05:56 UTC  
> Updated at: 2022-09-15 19:48:45 UTC  
> Closed at: 2022-09-15 19:48:45 UTC  
> Url: https://github.com/boostorg/url/issues/396  

This fails:  
```  
url u("/.//my:sharona");  
u.normalize();  
assert(u.string() == "/.//my:sharona");  
```
