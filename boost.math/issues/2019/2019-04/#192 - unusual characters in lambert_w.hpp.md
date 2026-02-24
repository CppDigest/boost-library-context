# #192 - unusual characters in lambert_w.hpp [Closed]

> Username: dittons  
> Created at: 2019-04-03 21:01:28 UTC  
> Updated at: 2019-04-04 11:23:31 UTC  
> Closed at: 2019-04-04 00:11:05 UTC  
> Url: https://github.com/boostorg/math/issues/192  

Compiling boost-math fails under MSVC when using the /utf-8 command-line option. (This tells it to assume the source files are utf8 encoded.)  
The error occurs on line 1865 of lambert_w.hpp which contains code page 1252 smart quotes (0x93, 0x94) and an en-dash (0x96) -  
`// On the Lambert W function,  Adv.Comput.Math., vol. 5, pp. 329359, 1996.`  
  
These 3 bytes, being followed by values in the range 0x00-0x7F, are invalid in utf8 encoding and hence the compiler complains.  
  
Are boost source files specified to use Windows 1252 code page, or should compiling assuming utf-8 work ?

---

## Comment 1

> Username: NAThompson  
> Created at: 2019-04-03 23:42:16 UTC  
> Url: https://github.com/boostorg/math/issues/192#issuecomment-479696843  

Fixed in 166e5fb5130ac76d38c4056236e7f408b009702e

---

## Comment 2

> Username: jzmaddock  
> Created at: 2019-04-04 07:51:51 UTC  
> Url: https://github.com/boostorg/math/issues/192#issuecomment-479790190  

Thanks Nick!

---

## Comment 3

> Username: NAThompson  
> Created at: 2019-04-04 11:23:31 UTC  
> Url: https://github.com/boostorg/math/issues/192#issuecomment-479857888  

No problem; should this be cherry picked into the release? It's strange that no one else hit it before.
