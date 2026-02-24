# #46 Fix two cases of redefinition and hiding of local variables [Merged]

> Username: cspanier  
> Created at: 2022-11-10 09:11:01 UTC  
> Updated at: 2022-11-14 06:04:53 UTC  
> Merged at: 2022-11-14 06:04:53 UTC  
> Closed at: 2022-11-14 06:04:53 UTC  
> Url: https://github.com/boostorg/exception/pull/46  

There is a minor issue in `boost/exception/diagnostic_information.hpp` that causes the following two MSVC compiler warnings:  
  
```  
(...)\boost\exception\diagnostic_information.hpp(152): warning C6246: Local declaration of 'l' hides declaration of the same name in outer scope. For additional information, see previous declaration at line '143' of '(...)\boost\exception\diagnostic_information.hpp'.: Lines: 143  
(...)\boost\exception\diagnostic_information.hpp(156): warning C6246: Local declaration of 'fn' hides declaration of the same name in outer scope. For additional information, see previous declaration at line '144' of '(...)\boost\exception\diagnostic_information.hpp'.: Lines: 144  
```  
  
Clearly, the outer l and fn can be reused within the else branch.

---

## Comment 1

> Username: zajo  
> Created_at: 2022-11-14 00:05:07 UTC  
> Url: https://github.com/boostorg/exception/pull/46#issuecomment-1312861549  

Thanks for the pull request, but your changes were not correct. I renamed the local variables to deal with the warnings, see https://github.com/boostorg/exception/commit/0570b700174789f017114b2c8e2e6686323eb89d.

---

## Comment 2

> Username: cspanier  
> Created_at: 2022-11-14 05:41:20 UTC  
> Url: https://github.com/boostorg/exception/pull/46#issuecomment-1313125124  

I don't see why the second identical call should yield a different result just a few lines below. [`get_error_info`](https://www.boost.org/doc/libs/1_80_0/libs/exception/doc/get_error_info.html) doesn't have documented side effects and, other than that, there is only a pointer comparison and `operator<<(ostringstream&, char const *)` in between. Would you be so kind as to briefly explain what I have overlooked? Just for the sake of interest.  
  
Thanks for fixing! I've seen this warning for many Boost versions.

---

## Comment 3

> Username: zajo  
> Created_at: 2022-11-14 06:04:41 UTC  
> Url: https://github.com/boostorg/exception/pull/46#issuecomment-1313139540  

Yes you are right, my bad. Thanks!

---
