# #238 - emscripten support? [Closed]

> Username: unicomp21  
> Created at: 2020-01-26 17:04:02 UTC  
> Updated at: 2020-04-24 09:49:54 UTC  
> Closed at: 2020-04-24 09:49:53 UTC  
> Url: https://github.com/boostorg/fiber/issues/238  

Given webassembly currently doesn't have couroutine support, being able to use boost fibers from emscripten would be awesome!!!

---

## Comment 1

> Username: unicomp21  
> Created at: 2020-01-26 19:17:39 UTC  
> Url: https://github.com/boostorg/fiber/issues/238#issuecomment-578533445  

I believe clang can emit webassembly now, could we have webassembly target?

---

## Comment 2

> Username: Akaricchi  
> Created at: 2020-02-13 18:12:47 UTC  
> Url: https://github.com/boostorg/fiber/issues/238#issuecomment-585896575  

For the record, Emscripten now has [a low-level fiber API](https://emscripten.org/docs/api_reference/fiber.h.html) since 1.39.7. It could be used as a backend for boost fibers.

---

## Comment 3

> Username: olk  
> Created at: 2020-04-24 09:49:53 UTC  
> Url: https://github.com/boostorg/fiber/issues/238#issuecomment-618915778  

belongs to boost.context
