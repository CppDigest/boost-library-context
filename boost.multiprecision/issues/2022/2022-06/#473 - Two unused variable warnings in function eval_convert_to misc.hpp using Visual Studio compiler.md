# #473 - Two unused variable warnings in function eval_convert_to misc.hpp using Visual Studio compiler [Closed]

> Username: MonocleSecurity  
> Created at: 2022-06-08 21:39:23 UTC  
> Updated at: 2022-06-18 15:18:44 UTC  
> Closed at: 2022-06-18 15:18:44 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/473  

https://github.com/boostorg/multiprecision/blob/9f55ad5007cc5a041516da3b945901a53ba94023/include/boost/multiprecision/cpp_int/misc.hpp#L131  
  
If the following BOOST_IF_CONSTEXPR(line 132) is false, then both variables "shift" and "i" are unused and produce warning C4189 using the Visual Studio 2019 compiler. When compiling with warnings as errors, this is a particularly annoying problem because you must disable warnings around every instance of the templated function.  
  
I believe a simple solution is to move the two variables inside the if statement block and the functionality will remain the same.

---

## Comment 1

> Username: ckormanyos  
> Created at: 2022-06-09 08:19:31 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/473#issuecomment-1150819078  

We have been handling warnings in this current trimester. I'll check if we did or did not already handle these and be sure to integrate a fix for 1.80. Many thanks for your report.

---

## Comment 2

> Username: ckormanyos  
> Created at: 2022-06-11 10:13:06 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/473#issuecomment-1152893908  

> a simple solution is to move the two variables inside the if statement block and the functionality will remain the same  
  
Yep.  
  
I ended up adding relevant changes form this issue and scanned all of Multiprecision for similar cases of scopes that can be reduced. Found about ten things at the end of it.  
  
The changes, including those that handle this issue, are cycling in #471.
