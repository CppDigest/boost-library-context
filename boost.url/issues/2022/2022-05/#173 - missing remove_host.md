# #173 - missing remove_host [Closed]

> Username: vinniefalco  
> Created at: 2022-05-17 17:07:25 UTC  
> Updated at: 2022-05-17 20:19:16 UTC  
> Closed at: 2022-05-17 17:32:20 UTC  
> Url: https://github.com/boostorg/url/issues/173  

There's no `url::remove_host`?

---

## Comment 1

> Username: alandefreitas  
> Created at: 2022-05-17 17:32:20 UTC  
> Url: https://github.com/boostorg/url/issues/173#issuecomment-1129135314  

We cannot remove the host without removing the authority ([`u.remove_authority()`](https://develop.url.cpp.al/url/ref/boost__urls__url/remove_authority.html)).  
  
```  
http://user:pass@boost.org:80/users/download  
->   
http://user:pass@:80/users/download  
```  
  
This is an empty host ([`set_host("")`](https://develop.url.cpp.al/url/ref/boost__urls__url/set_host.html)) but the host still exists.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2022-05-17 20:19:16 UTC  
> Url: https://github.com/boostorg/url/issues/173#issuecomment-1129280341  

derrrrrp
