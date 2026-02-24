# #1217 - Review 2: normalize docs [Closed]

> Username: jzmaddock  
> Created at: 2025-11-05 19:35:21 UTC  
> Updated at: 2026-01-29 15:22:52 UTC  
> Closed at: 2025-11-14 13:31:12 UTC  
> Url: https://github.com/boostorg/decimal/issues/1217  

> I understand that IEEE defines what the normalized form is but we cannot expect  
users of this library to all have access to the text of that standard.  I  
recommend describing what a normalized decimal is in the suggested vocabulary  
section and link to that section from here.  Or describe it briefly in the  
vocabulary and link for detailed explanation to this function and its  
documentation (and of course give the detailed explanation here).  
  
At the very least, users need to be made aware that there exists multiple forms of the same number and a std-mandated normalized form.

---

## Comment 1

> Username: mborland  
> Created at: 2025-11-06 08:25:59 UTC  
> Url: https://github.com/boostorg/decimal/issues/1217#issuecomment-3495814929  

It's unfortunate there's no official unofficial version of IEEE 754 like there is the C++ standard.
