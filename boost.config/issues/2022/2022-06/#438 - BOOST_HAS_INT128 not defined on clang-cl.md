# #438 - BOOST_HAS_INT128 not defined on clang-cl [Closed]

> Username: pdimov  
> Created at: 2022-06-22 01:22:03 UTC  
> Updated at: 2022-08-31 09:11:28 UTC  
> Closed at: 2022-08-31 09:11:28 UTC  
> Url: https://github.com/boostorg/config/issues/438  

https://github.com/boostorg/config/blob/1cff5e37bb69f42ffab73438de61e34c71258f19/include/boost/config/compiler/clang.hpp#L101-L103 checks for clang-cl and does not define BOOST_HAS_INT128 if so. But clang-cl does have __int128 (under 64 bit, as usual.) Why are we not defining HAS_INT128?

---

## Comment 1

> Username: Lastique  
> Created at: 2022-08-30 20:01:20 UTC  
> Url: https://github.com/boostorg/config/issues/438#issuecomment-1232107517  

Note that even the "usual" clang has [problems](https://github.com/boostorg/atomic/blob/581dd7953f467c938aeb7d17f9ff32e3269e3fbe/.github/workflows/ci.yml#L241) with `__int128`. If it is to be enabled for clang-cl, the support needs to be tested, and there should probably be a compiler version check.

---

## Comment 2

> Username: pdimov  
> Created at: 2022-08-30 22:54:25 UTC  
> Url: https://github.com/boostorg/config/issues/438#issuecomment-1232252297  

I think that the reason the macro isn't defined for clang-cl is that library support is missing (but I have no idea what this library support entails.)  
  
But this isn't useful when you just want to know whether the type exists (along with the built-in arithmetic operations of course) - which it does under clang-cl.

---

## Comment 3

> Username: jzmaddock  
> Created at: 2022-08-31 07:29:27 UTC  
> Url: https://github.com/boostorg/config/issues/438#issuecomment-1232566772  

The thing is, __int128 *is a* library.  So yes the type exists, and it will compile arithmetic operations, but then you get linker errors because the actual software routines which perform say division aren't available.  So in practice you can't actually use the type in a program... unless you know otherwise?

---

## Comment 4

> Username: pdimov  
> Created at: 2022-08-31 09:11:28 UTC  
> Url: https://github.com/boostorg/config/issues/438#issuecomment-1232679253  

You're right, division doesn't work on clang-cl (undefined reference to `__divti3`.)
