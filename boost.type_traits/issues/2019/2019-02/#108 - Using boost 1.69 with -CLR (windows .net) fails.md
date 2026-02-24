# #108 - Using boost 1.69 with /CLR (windows .net) fails [Closed]

> Username: jbulow  
> Created at: 2019-02-13 19:49:29 UTC  
> Updated at: 2019-02-14 11:06:56 UTC  
> Closed at: 2019-02-14 11:06:56 UTC  
> Url: https://github.com/boostorg/type_traits/issues/108  

Triggers this error:  
  
```  
Warning	C4561	  
'__fastcall' incompatible with the '/clr' option: converting to '__stdcall'  
...\include\boost\type_traits\detail\is_member_function_pointer_cxx_11.hpp	120	  
  
Error	C2953	  
'boost::is_member_function_pointer<Ret(__stdcall C::* )(Args...)>': class template has already been defined  
...\include\boost\type_traits\detail\is_member_function_pointer_cxx_11.hpp	120	  
  
```  
  
The fix is attached.  
[clr_patch.txt](https://github.com/boostorg/system/files/2861930/clr_patch.txt)

---

## Comment 1

> Username: pdimov  
> Created at: 2019-02-13 21:06:54 UTC  
> Url: https://github.com/boostorg/type_traits/issues/108#issuecomment-463370878  

The inclusion of what Boost headers triggers this error for you?

---

## Comment 2

> Username: jbulow  
> Created at: 2019-02-13 21:38:39 UTC  
> Url: https://github.com/boostorg/type_traits/issues/108#issuecomment-463383760  

Any header that includes the affected files I guess. I don't know an easy way to find out how an include files was included in the first place when using visual studio. I don't think it does matter. It is an obvious error with an obvious fix. The _MANAGED guard could be narrowed down to the _fastcall declarations maybe.

---

## Comment 3

> Username: pdimov  
> Created at: 2019-02-13 21:42:41 UTC  
> Url: https://github.com/boostorg/type_traits/issues/108#issuecomment-463385324  

Yes but the error is in Type Traits and your fix refers to Type Traits, but you've opened an issue against Boost.System, which is why I was wondering whether there's something to fix in Boost.System.  
  
I'll transfer the issue to Type Traits then.

---

## Comment 4

> Username: jzmaddock  
> Created at: 2019-02-14 11:06:55 UTC  
> Url: https://github.com/boostorg/type_traits/issues/108#issuecomment-463587600  

Duplicates: https://github.com/boostorg/type_traits/issues/99  
  
Fixed in master and develop.
