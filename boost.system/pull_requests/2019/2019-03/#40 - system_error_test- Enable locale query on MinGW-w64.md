# #40 system_error_test: Enable locale query on MinGW-w64 [Merged]

> Username: Kojoley  
> Created at: 2019-03-16 18:43:22 UTC  
> Updated at: 2019-03-17 11:28:18 UTC  
> Merged at: 2019-03-17 09:28:46 UTC  
> Closed at: 2019-03-17 09:28:46 UTC  
> Url: https://github.com/boostorg/system/pull/40  



---

## Comment 1

> Username: pdimov  
> Created_at: 2019-03-16 21:34:24 UTC  
> Url: https://github.com/boostorg/system/pull/40#issuecomment-473594573  

`__MINGW32__` - any MinGW  
`__MINGW64__` - MinGW-w64 in 64 bit mode  
`__MINGW64_VERSION_MAJOR` - MinGW-w64 in both 64 and 32 bit modes

---

## Comment 2

> Username: Kojoley  
> Created_at: 2019-03-16 21:53:12 UTC  
> Url: https://github.com/boostorg/system/pull/40#issuecomment-473595724  

That's macro naming is... confusing. Also, MSYS2 which is Cygwin has `GetUserDefaultUILanguage` in my case, change Cygwin check to `!defined(__CYGWIN__) || defined(__MSYS__)`?

---

## Comment 3

> Username: pdimov  
> Created_at: 2019-03-16 22:41:59 UTC  
> Url: https://github.com/boostorg/system/pull/40#issuecomment-473598612  

I don't think `__MSYS__` matters. I have `GetUserDefaultUILanguage` everywhere - in Cygwin, Cygwin 64, MinGW-w64, MinGW 32. These checks seem outdated.

---

## Comment 4

> Username: Kojoley  
> Created_at: 2019-03-16 22:47:50 UTC  
> Url: https://github.com/boostorg/system/pull/40#issuecomment-473598951  

For Cygwin it seems outdated for 19 years https://cygwin.com/git/gitweb.cgi?p=newlib-cygwin.git;a=commit;h=48ba505b8bc8671c7a2b243c816bf6f5b4451291, for MinGW-w64 7-12 years, I am not sure about MinGW. Should I remove them and leave only `!BOOST_PLAT_WINDOWS_RUNTIME`?

---

## Comment 5

> Username: pdimov  
> Created_at: 2019-03-16 22:52:41 UTC  
> Url: https://github.com/boostorg/system/pull/40#issuecomment-473599180  

Yes. Appveyor has all these so if it complains, we'll put them back in, but I doubt it.

---
