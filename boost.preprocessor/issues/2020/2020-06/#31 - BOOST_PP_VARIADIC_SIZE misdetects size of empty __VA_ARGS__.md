# #31 - BOOST_PP_VARIADIC_SIZE misdetects size of empty __VA_ARGS__ [Closed]

> Username: Lastique  
> Created at: 2020-06-04 17:13:32 UTC  
> Updated at: 2021-11-05 19:26:56 UTC  
> Closed at: 2020-12-14 16:16:37 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/31  

Consider the following test case:  
  
```  
#include <iostream>  
#include <boost/preprocessor/variadic/size.hpp>  
  
#define FOO(...) BOOST_PP_VARIADIC_SIZE(__VA_ARGS__)  
  
int main()  
{  
    std::cout << "0: " << FOO() << ", 1: " << FOO(x) << ", 2: " << FOO(x, y) << ", 3: " << FOO(x, y, z) << std::endl;  
}  
```  
  
With gcc 9.3, `g++ -std=c++17 -o pp_variadic_size pp_variadic_size.cpp`, it outputs:  
  
```  
0: 1, 1: 1, 2: 2, 3: 3  
```  
  
The expected output for `FOO()` is 0.  
  
I'm not sure if empty varargs are supported by Boost.Preprocessor, but the `BOOST_PP_VARIADIC_SIZE` documentation makes no exceptions in this regard, so I assume it should work.

---

## Comment 1

> Username: eldiener  
> Created at: 2020-06-06 16:02:17 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/31#issuecomment-640082298  

You are correct. I do support C++20 when I can detect it, with the support I added for the `__VA_OPT__` construct, so I should return 0 for the BOOST_PP__VARIADIC_SIZE in C++20 mode when the variadic data is empty. I will correct this for the next release and update the 'develop' branch first with my correction. Thanks for bringing this up. I do not know how I missed this when I added `__VA_OPT__` support for the 1.73 release.

---

## Comment 2

> Username: Lastique  
> Created at: 2020-06-06 16:25:07 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/31#issuecomment-640085150  

Is the support not possible for pre-C++20? If not, please add a note in the docs so that users are aware of the limitation.

---

## Comment 3

> Username: eldiener  
> Created at: 2020-06-06 16:36:32 UTC  
> Updated at: 2020-06-06 16:37:48 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/31#issuecomment-640086676  

It is possible for variadic data to be empty. The problem is that prior to C++20, through the `__VA_OPT__` construct, there was no 100% failsafe way of determining if it was empty or not. That is the reason why returning 0 for the BOOST_PP_VARIADIC_SIZE when the data was empty was never implemented. I will add a further note to that effect in the docuentation to the macro when I correct the implementation. I do realize that a number of compiler implementations support the `__VA_OPT__` construct, and therefore the failsafe way of determing whether variadic data is empty, even when they are not being used in C++20 mode. But determining when this happens, and what compiler options have to be in effect when this happens, is more than I want or can do with the preprocessor library.

---

## Comment 4

> Username: eldiener  
> Created at: 2020-06-30 19:00:08 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/31#issuecomment-651983274  

I have updated the 'develop' branch with a fix which in which BOOST_PP_VARIADIC_SIZE expands to 0 when empty data is passed to it in C++ 20 mode when __VA_OPT__ is supported. This had large ramifications in the library, which entailed other fixes. Along with being able to pass empty data to BOOST_PP_VARIADIC_SIZE in C++20 mode, I also have allowed in that same mode conversions between empty variadic data and Boost PP arrays and lists, either of which can be empty. I updated the implementation and the docs with this change for C++ 20 mode. I do not think the change should break any outside code since prior ro C++20 passing empty data to variadic macros should not have been valid in any situation. The Travis CI and Appveyor tests have passed, and if the 'develop' regression tests look good I will try to merge this change to 'master' before the 1.74 release.

---

## Comment 5

> Username: Lastique  
> Created at: 2020-06-30 20:38:42 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/31#issuecomment-652030136  

Thank you Edward.

---

## Comment 6

> Username: eldiener  
> Created at: 2020-07-04 14:20:55 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/31#issuecomment-653771938  

I have decided to wait until the 1.75 release to incorporate this change into the 'master' branch. It is too late now and I do not want to upset the 1.74 release process by incorporating this change. Once the 1.74 release is officially out and the 'master' branch is open to significant changes I will update the 'master' branch. Boost PP is too significantly used by other Boost libraries to make a change at the very end of a release cycle when the 'master' branch has been closed to all major changes.

---

## Comment 7

> Username: eldiener  
> Created at: 2020-08-27 22:56:29 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/31#issuecomment-682231740  

This change is now in the 'master' branch and will therefore automatically be part of Boost 1.75.

---

## Comment 8

> Username: eldiener  
> Created at: 2020-12-14 16:16:37 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/31#issuecomment-744547280  

The change is in Boost 1.75, so I will close this issue.
