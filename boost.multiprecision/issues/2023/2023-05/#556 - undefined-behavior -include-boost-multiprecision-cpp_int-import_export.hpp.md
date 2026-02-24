# #556 - undefined-behavior /include/boost/multiprecision/cpp_int/import_export.hpp [Closed]

> Username: Wayonb  
> Created at: 2023-05-22 18:24:49 UTC  
> Updated at: 2023-08-28 02:41:49 UTC  
> Closed at: 2023-08-28 02:41:49 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/556  

While running our unit tests with UndefinedBehaviorSanitizer, we hit an error condition.  The error did not seems to be related to our code so I took first example and it reproduce the same error.  
https://www.boost.org/doc/libs/1_80_0/libs/multiprecision/doc/html/boost_multiprecision/tut/import_export.html#boost_multiprecision.tut.import_export.examples  
  
Is this is a known issue or am I missing something?  
```  
/mybuild/include/boost/multiprecision/cpp_int/import_export.hpp:112:54: runtime error: implicit conversion from type 'difference_type' (aka 'long') of value -8 (64-bit, signed) to type 'unsigned long' changed the value to 18446744073709551608 (64-bit, unsigned)  
SUMMARY: UndefinedBehaviorSanitizer: undefined-behavior /mybuild/include/boost/multiprecision/cpp_int/import_export.hpp:112:54 in   
/mybuild/include/boost/multiprecision/cpp_int/import_export.hpp:112:42: runtime error: implicit conversion from type 'unsigned long' of value 18446744073709551608 (64-bit, unsigned) to type 'difference_type' (aka 'long') changed the value to -8 (64-bit, signed)  
SUMMARY: UndefinedBehaviorSanitizer: undefined-behavior /mybuild/include/boost/multiprecision/cpp_int/import_export.hpp:112:42 in   
```

---

## Comment 1

> Username: mborland  
> Created at: 2023-05-24 10:41:11 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/556#issuecomment-1560882493  

What version of boost are you using? The docs link is 1.80, and there was a [fix applied in 1.81](https://github.com/boostorg/multiprecision/issues/488). On current develop with GCC-13.1.1 on Fedora 38 I can not replicate the UBSAN error with the linked example.

---

## Comment 2

> Username: Wayonb  
> Created at: 2023-05-24 13:49:47 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/556#issuecomment-1561190334  

> What version of boost are you using? The docs link is 1.80, and there was a [fix applied in 1.81](https://github.com/boostorg/multiprecision/issues/488). On current develop with GCC-13.1.1 on Fedora 38 I can not replicate the UBSAN error with the linked example.  
  
@mborland Thanks for the quick reply.  
We had another issues with boost 1.81 which prevented us from upgrade.  I see boost 1.82 is also release but will try upgrading after Conan package is released.

---

## Comment 3

> Username: ckormanyos  
> Created at: 2023-08-23 04:31:43 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/556#issuecomment-1689265027  

> will try upgrading after Conan package is released  
  
Hi @Wayonb I'm going through our Multiprecision issues. Do you have an updated report for this? Is the issue still present/known/active?

---

## Comment 4

> Username: Wayonb  
> Created at: 2023-08-28 02:41:49 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/556#issuecomment-1694920579  

@ckormanyos I will close until we have time to update.  Thanks.
