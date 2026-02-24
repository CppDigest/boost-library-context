# #23 - [MSVC] Boost\libs\container_hash failed to build [Closed]

> Username: QuellaZhang  
> Created at: 2022-06-13 09:47:44 UTC  
> Updated at: 2022-07-10 15:22:10 UTC  
> Closed at: 2022-07-10 15:22:10 UTC  
> Url: https://github.com/boostorg/container_hash/issues/23  

**Issue description:**  
Boost\libs\container_hash failed to build due to  warning C4996 on MSVC, regress by https://github.com/microsoft/STL/pull/2759. Could you please take a look?  
  
**Build step:**  
  
1. git clone -c core.autocrlf=true --recursive ​https://github.com/boostorg/boost.git F:\gitP\boostorg\boost  
2. open a VS2022 x86 command prompt and browse to F:\gitP\boostorg\boost  
3. .\bootstrap  
4. .\b2 headers variant=release --build-dir=out\x86rel address-model=32  
5. .\b2 variant=release --build-dir=out\x86rel address-model=32  
6. .\b2 -j16 variant=release --build-dir=..\out\x86rel libs\container_hash\test  
  
**Error info:**  
hash_complex_test.cpp  
libs\container_hash\test\hash_complex_test.cpp(83): error C2220: the following warning is treated as an error  
libs\container_hash\test\hash_complex_test.cpp(83): warning C4996: 'std::complex<Integer>::complex': warning STL4037: The effect of instantiating the template std::complex for any type other than float, double, or long double is unspecified. You can define _SILENCE_NONFLOATING_COMPLEX_DEPRECATION_WARNING to acknowledge that you have received this warning.  
        with  
        [  
            Integer=int  
        ]  
libs\container_hash\test\hash_complex_test.cpp(101): note: see reference to function template instantiation 'void complex_integral_tests<int>(Integer *)' being compiled  
        with  
        [  
            Integer=int  
        ]  
libs\container_hash\test\hash_complex_test.cpp(84): warning C4996: 'std::complex<Integer>::complex': warning STL4037: The effect of instantiating the template std::complex for any type other than float, double, or long double is unspecified. You can define _SILENCE_NONFLOATING_COMPLEX_DEPRECATION_WARNING to acknowledge that you have received this warning.  
        with  
        [  
            Integer=int  
        ]  
libs\container_hash\test\hash_complex_test.cpp(85): warning C4996: 'std::complex<Integer>::complex': warning STL4037: The effect of instantiating the template std::complex for any type other than float, double, or long double is unspecified. You can define _SILENCE_NONFLOATING_COMPLEX_DEPRECATION_WARNING to acknowledge that you have received this warning.  
        with  
        [  
            Integer=int  
        ]  
libs\container_hash\test\hash_complex_test.cpp(86): warning C4996: 'std::complex<Integer>::complex': warning STL4037: The effect of instantiating the template std::complex for any type other than float, double, or long double is unspecified. You can define _SILENCE_NONFLOATING_COMPLEX_DEPRECATION_WARNING to acknowledge that you have received this warning.  
        with  
        [  
            Integer=int  
        ]  
libs\container_hash\test\hash_complex_test.cpp(87): warning C4996: 'std::complex<Integer>::complex': warning STL4037: The effect of instantiating the template std::complex for any type other than float, double, or long double is unspecified. You can define _SILENCE_NONFLOATING_COMPLEX_DEPRECATION_WARNING to acknowledge that you have received this warning.  
        with  
        [  
            Integer=int  
        ]  
libs\container_hash\test\hash_complex_test.cpp(88): warning C4996: 'std::complex<Integer>::complex': warning STL4037: The effect of instantiating the template std::complex for any type other than float, double, or long double is unspecified. You can define _SILENCE_NONFLOATING_COMPLEX_DEPRECATION_WARNING to acknowledge that you have received this warning.  
        with  
        [  
            Integer=int  
        ]  
libs\container_hash\test\hash_complex_test.cpp(83): warning C4996: 'std::complex<Integer>::complex': warning STL4037: The effect of instantiating the template std::complex for any type other than float, double, or long double is unspecified. You can define _SILENCE_NONFLOATING_COMPLEX_DEPRECATION_WARNING to acknowledge that you have received this warning.  
        with  
        [  
            Integer=long  
        ]  
libs\container_hash\test\hash_complex_test.cpp(102): note: see reference to function template instantiation 'void complex_integral_tests<long>(Integer *)' being compiled  
        with  
        [  
            Integer=long

---

## Comment 1

> Username: pdimov  
> Created at: 2022-06-13 11:27:45 UTC  
> Url: https://github.com/boostorg/container_hash/issues/23#issuecomment-1153799799  

Should be fixed now, but shouldn't you be getting the same error in `hash_reference_values.cpp`, which also uses `std::complex<int>`?

---

## Comment 2

> Username: QuellaZhang  
> Created at: 2022-06-14 02:36:57 UTC  
> Url: https://github.com/boostorg/container_hash/issues/23#issuecomment-1154644196  

There are no more errors than the following errors.  
  
```  
hash_reference_values.cpp  
libs\container_hash\test\hash_reference_values.cpp(340): error C2220: the following warning is treated as an error  
libs\container_hash\test\hash_reference_values.cpp(340): warning C4996: 'std::complex<int>::complex': warning STL4037: The effect of instantiating the template std::complex for any type other than float, double, or long double is unspecified. You can define _SILENCE_NONFLOATING_COMPLEX_DEPRECATION_WARNING to acknowledge that you have received this warning.  
libs\container_hash\test\hash_reference_values.cpp(341): warning C4996: 'std::complex<int>::complex': warning STL4037: The effect of instantiating the template std::complex for any type other than float, double, or long double is unspecified. You can define _SILENCE_NONFLOATING_COMPLEX_DEPRECATION_WARNING to acknowledge that you have received this warning.  
libs\container_hash\test\hash_reference_values.cpp(342): warning C4996: 'std::complex<int>::complex': warning STL4037: The effect of instantiating the template std::complex for any type other than float, double, or long double is unspecified. You can define _SILENCE_NONFLOATING_COMPLEX_DEPRECATION_WARNING to acknowledge that you have received this warning.  
libs\container_hash\test\hash_reference_values.cpp(351): warning C4996: 'std::complex<int>::complex': warning STL4037: The effect of instantiating the template std::complex for any type other than float, double, or long double is unspecified. You can define _SILENCE_NONFLOATING_COMPLEX_DEPRECATION_WARNING to acknowledge that you have received this warning.  
libs\container_hash\test\hash_reference_values.cpp(352): warning C4996: 'std::complex<int>::complex': warning STL4037: The effect of instantiating the template std::complex for any type other than float, double, or long double is unspecified. You can define _SILENCE_NONFLOATING_COMPLEX_DEPRECATION_WARNING to acknowledge that you have received this warning.  
```

---

## Comment 3

> Username: pdimov  
> Created at: 2022-06-14 12:39:02 UTC  
> Url: https://github.com/boostorg/container_hash/issues/23#issuecomment-1155130705  

This should be fixed now as well.
