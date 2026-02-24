# #1730 - boost/beast/ssl.hpp not included in installed version of vcpkg [Closed]

> Username: illera88  
> Created at: 2019-10-10 21:46:37 UTC  
> Updated at: 2019-10-10 22:16:43 UTC  
> Closed at: 2019-10-10 22:16:43 UTC  
> Url: https://github.com/boostorg/beast/issues/1730  

Hi,  
  
I'm trying to use beast after installing it with vcpkg:  
`vcpkg.exe install boost:x86-windows-static`  
  
Unfortunately it seems that it does not include the header `boost/beast/ssl.hpp` which is needed to compile a ssl websocket server.   
  
Is this an expected behavior?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-10-10 21:47:06 UTC  
> Url: https://github.com/boostorg/beast/issues/1730#issuecomment-540813857  

No idea why that file is not there for you. What version is vcpkg installing?

---

## Comment 2

> Username: illera88  
> Created at: 2019-10-10 21:52:52 UTC  
> Url: https://github.com/boostorg/beast/issues/1730#issuecomment-540815558  

`boost-beast:x86-windows-static                     1.69.0           Boost beast module`

---

## Comment 3

> Username: vinniefalco  
> Created at: 2019-10-10 22:00:11 UTC  
> Url: https://github.com/boostorg/beast/issues/1730#issuecomment-540817623  

Are you using the 1.71 examples with the 1.69 package? You have to use the examples from 1.69. You can get them using the tagged release:  
https://github.com/boostorg/beast/tree/boost-1.69.0/example  
  
However I suggest you use 1.71 if possible, it has some great features like built-in websocket timeouts and various improvements. But 1.69 will still work well for you.

---

## Comment 4

> Username: illera88  
> Created at: 2019-10-10 22:16:43 UTC  
> Url: https://github.com/boostorg/beast/issues/1730#issuecomment-540822240  

You are right. I was mixing versions. It seems that the vcpkg boost version is 1.69 and I was using that boost against beast examples from the 1.71 version.  
  
Thanks
