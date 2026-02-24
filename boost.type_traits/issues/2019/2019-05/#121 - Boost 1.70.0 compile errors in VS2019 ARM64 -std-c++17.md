# #121 - Boost 1.70.0 compile errors in VS2019 ARM64 /std:c++17 [Closed]

> Username: gongminmin  
> Created at: 2019-05-05 21:35:00 UTC  
> Updated at: 2019-09-15 21:10:03 UTC  
> Closed at: 2019-09-15 21:10:03 UTC  
> Url: https://github.com/boostorg/type_traits/issues/121  

It's similar to #118, but not exactly the same. When including the headers of type_traits, the error messages are:  
.../boost/type_traits/detail/is_function_cxx_11.hpp(386): error C2953: 'boost::is_function<Ret(Args...) noexcept>': class template has already been defined  
.../boost/type_traits/detail/is_function_cxx_11.hpp(316): note: see declaration of 'boost::is_function<Ret(Args...) noexcept>'  
...  
  
Looks like the compiler takes __cdecl, __stdcall, and __fastcall the same thing.

---

## Comment 1

> Username: gongminmin  
> Created at: 2019-05-05 22:00:09 UTC  
> Url: https://github.com/boostorg/type_traits/issues/121#issuecomment-489467136  

Figured that out. The line 379 of is_function_cxx_11.hpp and line 422 of is_member_function_pointer_cxx_11.hpp need to be changed from  
#ifdef _MSC_VER  
to  
#if defined(_MSC_VER) && !defined(_M_ARM) && !defined(_M_ARM64)  
  
Do you think it would be even better if the "#ifndef _M_AMD64" in those 2 files can be changed to "#ifdef _M_X86"? Because x86 is the only architecture can run into that condition. And also, only x86 distinguishes __cdecl, __stdcall, or __fastcall.  
  
If you are OK with it, I can send out a PR to fix this.
