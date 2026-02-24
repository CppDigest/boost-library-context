# #55 - ICU_LINK option does not work anymore [Closed]

> Username: nenomius  
> Created at: 2020-04-14 08:57:39 UTC  
> Updated at: 2022-10-21 15:13:12 UTC  
> Closed at: 2022-10-19 12:25:15 UTC  
> Url: https://github.com/boostorg/locale/issues/55  

ICU_LINK option does not work now, because `boost.regex` errors build if sees it now.  
See https://github.com/boostorg/regex/commit/fc4dc17dc745a9fc4ab1849cef3710354a6b7782 and https://github.com/boostorg/regex/issues/89  
  
I would suggest moving ICU finding/configuration into shared file and using it from there like its done for zlib, bzip2, etc, and not doing the same thing in different ways.  
  
@jzmaddock

---

## Comment 1

> Username: leha-bot  
> Created at: 2022-10-21 12:10:14 UTC  
> Url: https://github.com/boostorg/locale/issues/55#issuecomment-1286877309  

Finally I can build boost locale in Android NDK without dirty hacks 🥳

---

## Comment 2

> Username: Flamefire  
> Created at: 2022-10-21 15:13:11 UTC  
> Url: https://github.com/boostorg/locale/issues/55#issuecomment-1287102317  

@leha-bot Does this mean you tested #120 already and can confirm it works for you?
