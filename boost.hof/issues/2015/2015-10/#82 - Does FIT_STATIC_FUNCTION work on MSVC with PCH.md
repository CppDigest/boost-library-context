# #82 - Does FIT_STATIC_FUNCTION work on MSVC with PCH? [Open]

> Username: akrzemi1  
> Created at: 2015-10-06 08:10:37 UTC  
> Updated at: 2016-03-06 23:45:06 UTC  
> Url: https://github.com/boostorg/hof/issues/82  

I do not have access to the newer versions of MSVC, but on the older ones (e.g., 10.0), when you put your libraries into precompiled headers, tricks with globals and external linkage do not work and cause strange compiler errors.  
  
Has this library been tested with precompiled headers on Windows?

---

## Comment 1

> Username: pfultz2  
> Created at: 2015-10-06 20:14:10 UTC  
> Url: https://github.com/boostorg/hof/issues/82#issuecomment-145984965  

> tricks with globals and external linkage  
  
I am not using any external linkage for global variables. Its all `static` or internal linkage.  
  
> Has this library been tested with precompiled headers on Windows?  
  
No, it hasn't. What flags do I need to add to enable PCH on MSVC? I will add it to the appveyor CI.

---

## Comment 2

> Username: akrzemi1  
> Created at: 2015-10-07 07:02:34 UTC  
> Url: https://github.com/boostorg/hof/issues/82#issuecomment-146096798  

Ok, I may be using imprecise terms here; nonetheless, I still suspect you may run into problems with PCH. I tried to 'emulate' a global with external linkage in a header-only library using a static member function of a class template (see here: https://github.com/boostorg/optional/blob/master/include/boost/none.hpp) and it causes a bug when used with MSVC with PCH. But I test it with VS10.   
  
I do not know how to do it with flags. I used the Visual Studio's (version 10) IDE. I can only refer you to online docs: https://msdn.microsoft.com/en-us/library/szfdksca.aspx  
  
Here is a page with PCH-specific options: https://msdn.microsoft.com/en-us/library/7zc28563.aspx  
But I suspect it is more than just these options, you need a dedicated CPP file for compiling the PCH.

---

## Comment 3

> Username: pfultz2  
> Created at: 2015-10-07 14:03:06 UTC  
> Url: https://github.com/boostorg/hof/issues/82#issuecomment-146204520  

> I tried to 'emulate' a global with external linkage in a header-only library using a static member function of a class template  
  
I use this on other compilers except MSVC. You can see [here](https://github.com/pfultz2/Fit/blob/master/fit/detail/static_const_var.h)). `FIT_NO_UNIQUE_STATIC_VAR` gets defined to `1` for MSVC and gcc 4.6, as I couldn't make it work on those compilers. I have heard that the linker for MSVC is aggressive and will remove internal symbols that are the same, anyways. So it may be better to not use the static member for MSVC.  
  
Were you able to make `none` work with PCH on MSVC?

---

## Comment 4

> Username: akrzemi1  
> Created at: 2015-10-08 06:52:58 UTC  
> Url: https://github.com/boostorg/hof/issues/82#issuecomment-146436839  

I was. I just accepted that I will have a new instantiation in each translation unit:  
  
``` c++  
// in a header file  
struct none_t {};  
const none_t none;  
```  
  
It may not work in your case though.  
  
In practice, I was able to figure out that in MSVC with PCH the only way to define an instance with unique address in a header-only library is to use a pointer or a reference to an inline functions:  
  
``` c++  
// in a header file  
struct aux_tag{};  
typedef void(&none_t)(aux_tag);  
inline void none(aux_tag) {}  
```  
  
But I do not know if it helps your case.
