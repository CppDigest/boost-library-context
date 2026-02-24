# #410 - Windows ARM64 build failing : intel_intrinsics  '_addcarry_u32': identifier not found` [Closed]

> Username: BaptRo  
> Created at: 2022-01-14 08:13:28 UTC  
> Updated at: 2022-01-14 09:50:47 UTC  
> Closed at: 2022-01-14 09:50:47 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/410  

Related to this bug: https://github.com/boostorg/multiprecision/pull/331   
  
When cross compiling for windows ARM64 with `vs-professional-2019-16.10.3-msvc-14.29.30036-winsdk-10.0.19041.0` I get the following error:  
```...\boost_1_78_0\boost/multiprecision/cpp_int/intel_intrinsics.hpp(118): error C3861: '_addcarry_u32': identifier not found```  
  
This appear after bumping from boost 1.71 to 1.78.   
  
That the same error as reported here: https://github.com/microsoft/vcpkg/issues/17312

---

## Comment 1

> Username: jzmaddock  
> Created at: 2022-01-14 08:55:02 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/410#issuecomment-1012927177  

Was this fixed by: https://github.com/boostorg/multiprecision/pull/406/files ?

---

## Comment 2

> Username: BaptRo  
> Created at: 2022-01-14 09:00:15 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/410#issuecomment-1012930507  

I think so indeed.   
Do you plan to release a 1.78.1 or it's gonna be shipped with 1.79 in the coming months ?

---

## Comment 3

> Username: jzmaddock  
> Created at: 2022-01-14 09:02:28 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/410#issuecomment-1012932105  

Probably the latter.

---

## Comment 4

> Username: BaptRo  
> Created at: 2022-01-14 09:50:47 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/410#issuecomment-1012966716  

Ok thank you for your precisions.  
Closing the issue as it's fixed on develop.
