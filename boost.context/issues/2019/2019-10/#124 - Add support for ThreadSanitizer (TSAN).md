# #124 - Add support for ThreadSanitizer (TSAN) [Closed]

> Username: alokpr  
> Created at: 2019-10-23 18:18:40 UTC  
> Updated at: 2021-07-13 19:08:11 UTC  
> Closed at: 2021-07-13 19:08:11 UTC  
> Url: https://github.com/boostorg/context/issues/124  

Clang recently added fiber support: https://reviews.llvm.org/D54889  
  
Please consider adding necessary instrumentation so that boost::Context can be used with TSAN.

---

## Comment 1

> Username: lenerd  
> Created at: 2019-10-24 10:31:18 UTC  
> Url: https://github.com/boostorg/context/issues/124#issuecomment-545855716  

TSan support in Boost.Context/Fiber would be great.  
  
I tried to do this myself but so far I have not been successful (cf. https://github.com/boostorg/fiber/issues/214#issuecomment-538018484_).

---

## Comment 2

> Username: olk  
> Created at: 2020-04-24 09:00:46 UTC  
> Url: https://github.com/boostorg/context/issues/124#issuecomment-618893493  

you are welcome to provide a patch

---

## Comment 3

> Username: olk  
> Created at: 2021-07-13 19:08:11 UTC  
> Url: https://github.com/boostorg/context/issues/124#issuecomment-879331168  

closing because of no progress/no patch provided
