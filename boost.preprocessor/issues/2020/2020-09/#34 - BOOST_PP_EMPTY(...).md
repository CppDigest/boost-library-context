# #34 - BOOST_PP_EMPTY(...) [Closed]

> Username: vinipsmaker2  
> Created at: 2020-09-30 14:22:21 UTC  
> Updated at: 2020-10-13 01:36:44 UTC  
> Closed at: 2020-10-13 01:36:43 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/34  

Could you change current definition from:  
  
```c  
#define BOOST_PP_EMPTY()  
```  
  
to:  
  
```c  
#define BOOST_PP_EMPTY(...)  
```  
  
This will make some of my patterns easier without the need to define my own `BOOST_PP_EMPTY` (which is what I'm doing right now).

---

## Comment 1

> Username: eldiener  
> Created at: 2020-09-30 14:35:26 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/34#issuecomment-701432010  

The Boost VMD library has BOOST_VMD_EMPTY(...), which does exactly what you want, but it is understandable that you would want the same functionality in Boost PP, especially because it has been decided that for the next Boost release the Boost PP library, like the current Boost VMD library, assumes variadic macro support in the preprocessor. So I will make the change in Boost PP in the 'develop' branch and then merge it to 'master' after it has been tested. Thanks for bringing this issue up.

---

## Comment 2

> Username: vinipsmaker2  
> Created at: 2020-09-30 14:40:34 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/34#issuecomment-701435229  

I was unaware of Boost.VMD. Thank you.

---

## Comment 3

> Username: eldiener  
> Created at: 2020-10-13 01:19:11 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/34#issuecomment-707426415  

I have decided to not make the change for the Boost preprocessor library. The reason for this as that the change can break compilation for those using the Visual C++ default preprocessor and a variadic version of BOOST_PP_EMPTY. The reason for the need for those using the Visual C++ default preprocessor to change their code if using a variadic version of BOOST_PP_EMPTY is explained in the documentation for BOOST_VMD_EMPTY in the topic "Generating emptiness and identity" in the VMD docs. Please use the BOOST_VMD_EMPTY macro, or the BOOST_VMD_IDENTITY macro, to achieve what you want to do. It is in the header file `boost/vmd/identity.hpp` and does not depend on any other functionality in the VMD library. If you have any further questions about its use, please ask.

---

## Comment 4

> Username: vinipsmaker2  
> Created at: 2020-10-13 01:36:43 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/34#issuecomment-707430954  

I'm already using `BOOST_VMD_EMPTY`. Thank you.
