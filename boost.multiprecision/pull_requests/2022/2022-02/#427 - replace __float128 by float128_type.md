# #427 replace __float128 by float128_type [Closed]

> Username: svigerske  
> Created at: 2022-02-01 05:42:24 UTC  
> Updated at: 2022-02-06 09:18:25 UTC  
> Closed at: 2022-02-06 09:18:25 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/427  

For cases where `float128_type` is not` __float128`, e.g., Intels `_Quad`.  
  
Fixes #411.

---

## Comment 1

> Username: svigerske  
> Created_at: 2022-02-01 05:46:01 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/427#issuecomment-1026498463  

I didn't succeed in running the tests with the b2 tools:  
```  
[/path/to/boost/libs/multiprecision/test]$ ../../../b2 toolset=intel  
/path/to/boost/tools/build/src/tools\intel-win.jam:58: in intel-win.init from module intel-win  
error: No intel compiler version found!  
/path/to/boost/tools/build/src/build\toolset.jam:44: in toolset.using from module toolset  
/path/to/boost/tools/build/src/tools\intel.jam:83: in intel.init from module intel  
/path/to/boost/tools/build/src/build\toolset.jam:44: in toolset.using from module toolset  
/path/to/boost/tools/build/src\build-system.jam:543: in process-explicit-toolset-requests from module build-system  
/path/to/boost/tools/build/src\build-system.jam:610: in load from module build-system  
/path/to/boost/tools/build/src/kernel\modules.jam:294: in import from module modules  
/path/to/boost/tools/build/src/kernel/bootstrap.jam:139: in boost-build from module  
/path/to/boost/boost-build.jam:17: in module scope from module  
```  
  
But I can compile test_arithmetic_float_128.cpp successfully now. Unfortunately, there are a number of errors when executing. I don't know if they are related to the changes from this pull-request, though:  
```  
[/path/to/boost]$ icl libs/multiprecision/test/test_arithmetic_float_128.cpp `ls -d1 libs/*/include/ | sed -e 's/^/-I /'` -I libs/numeric/conversion/include/ -Qoption,cpp,--extended_float_types -EHsc -GR  
Intel(R) C++ Intel(R) 64 Compiler Classic for applications running on Intel(R) 64, Version 2021.2.0 Build 20210228_000000  
Copyright (C) 1985-2021 Intel Corporation.  All rights reserved.  
  
test_arithmetic_float_128.cpp  
Microsoft (R) Incremental Linker Version 14.29.30133.0  
Copyright (C) Microsoft Corporation.  All rights reserved.  
  
-out:test_arithmetic_float_128.exe   
test_arithmetic_float_128.obj   
[/path/to/boost]$ ./test_arithmetic_float_128.exe   
Testing mixed arithmetic with type: class boost::multiprecision::number<struct boost::multiprecision::backends::float128_backend,0> and unsigned char  
Error in lexical cast  
Source type = class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> > "128.0000000000000000000000000000000031"  
Target type = unsigned __int64  
In function: test_mixed_numeric_limits  
libs/multiprecision/test/test_arithmetic.hpp:1921 Unexpected exception of type struct boost::wrapexcept<class boost::bad_lexical_cast>  
Errot message was: bad lexical cast: source type value could not be interpreted as target  
Testing mixed arithmetic with type: class boost::multiprecision::number<struct boost::multiprecision::backends::float128_backend,0> and signed char  
Testing mixed arithmetic with type: class boost::multiprecision::number<struct boost::multiprecision::backends::float128_backend,0> and signed char  
Error in lexical cast  
Source type = class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> > "-64.00000000000000000000000000000000308"  
Target type = __int64  
In function: test_negative_mixed_numeric_limits  
libs/multiprecision/test/test_arithmetic.hpp:1549 Unexpected exception of type struct boost::wrapexcept<class boost::bad_lexical_cast>  
Errot message was: bad lexical cast: source type value could not be interpreted as target  
Error in lexical cast  
Source type = class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> > "64.00000000000000000000000000000000308"  
Target type = __int64  
In function: test_mixed_numeric_limits  
libs/multiprecision/test/test_arithmetic.hpp:1921 Unexpected exception of type struct boost::wrapexcept<class boost::bad_lexical_cast>  
Errot message was: bad lexical cast: source type value could not be interpreted as target  
Testing mixed arithmetic with type: class boost::multiprecision::number<struct boost::multiprecision::backends::float128_backend,0> and char  
Testing mixed arithmetic with type: class boost::multiprecision::number<struct boost::multiprecision::backends::float128_backend,0> and char  
Error in lexical cast  
Source type = class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> > "-64.00000000000000000000000000000000308"  
Target type = __int64  
In function: test_negative_mixed_numeric_limits  
libs/multiprecision/test/test_arithmetic.hpp:1549 Unexpected exception of type struct boost::wrapexcept<class boost::bad_lexical_cast>  
Errot message was: bad lexical cast: source type value could not be interpreted as target  
Error in lexical cast  
Source type = class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> > "64.00000000000000000000000000000000308"  
Target type = __int64  
In function: test_mixed_numeric_limits  
libs/multiprecision/test/test_arithmetic.hpp:1921 Unexpected exception of type struct boost::wrapexcept<class boost::bad_lexical_cast>  
Errot message was: bad lexical cast: source type value could not be interpreted as target  
Testing mixed arithmetic with type: class boost::multiprecision::number<struct boost::multiprecision::backends::float128_backend,0> and short  
Testing mixed arithmetic with type: class boost::multiprecision::number<struct boost::multiprecision::backends::float128_backend,0> and short  
Error in lexical cast  
Source type = class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> > "-16383.99999999999999999999999999999906"  
Target type = __int64  
In function: test_negative_mixed_numeric_limits  
libs/multiprecision/test/test_arithmetic.hpp:1549 Unexpected exception of type struct boost::wrapexcept<class boost::bad_lexical_cast>  
Errot message was: bad lexical cast: source type value could not be interpreted as target  
Error in lexical cast  
Source type = class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> > "16383.99999999999999999999999999999906"  
Target type = __int64  
In function: test_mixed_numeric_limits  
libs/multiprecision/test/test_arithmetic.hpp:1921 Unexpected exception of type struct boost::wrapexcept<class boost::bad_lexical_cast>  
Errot message was: bad lexical cast: source type value could not be interpreted as target  
Testing mixed arithmetic with type: class boost::multiprecision::number<struct boost::multiprecision::backends::float128_backend,0> and unsigned short  
Error in lexical cast  
Source type = class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> > "32767.9999999999999999999999999999989"  
Target type = unsigned __int64  
In function: test_mixed_numeric_limits  
libs/multiprecision/test/test_arithmetic.hpp:1921 Unexpected exception of type struct boost::wrapexcept<class boost::bad_lexical_cast>  
Errot message was: bad lexical cast: source type value could not be interpreted as target  
Testing mixed arithmetic with type: class boost::multiprecision::number<struct boost::multiprecision::backends::float128_backend,0> and int  
Testing mixed arithmetic with type: class boost::multiprecision::number<struct boost::multiprecision::backends::float128_backend,0> and int  
Error in lexical cast  
Source type = class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> > "-1073741823.999999999999999999999999927"  
Target type = __int64  
In function: test_negative_mixed_numeric_limits  
libs/multiprecision/test/test_arithmetic.hpp:1549 Unexpected exception of type struct boost::wrapexcept<class boost::bad_lexical_cast>  
Errot message was: bad lexical cast: source type value could not be interpreted as target  
Error in lexical cast  
Source type = class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> > "1073741823.999999999999999999999999927"  
Target type = __int64  
In function: test_mixed_numeric_limits  
libs/multiprecision/test/test_arithmetic.hpp:1921 Unexpected exception of type struct boost::wrapexcept<class boost::bad_lexical_cast>  
Errot message was: bad lexical cast: source type value could not be interpreted as target  
Testing mixed arithmetic with type: class boost::multiprecision::number<struct boost::multiprecision::backends::float128_backend,0> and unsigned int  
Error in lexical cast  
Source type = class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> > "2147483647.999999999999999999999999855"  
Target type = unsigned __int64  
In function: test_mixed_numeric_limits  
libs/multiprecision/test/test_arithmetic.hpp:1921 Unexpected exception of type struct boost::wrapexcept<class boost::bad_lexical_cast>  
Errot message was: bad lexical cast: source type value could not be interpreted as target  
Testing mixed arithmetic with type: class boost::multiprecision::number<struct boost::multiprecision::backends::float128_backend,0> and long  
Testing mixed arithmetic with type: class boost::multiprecision::number<struct boost::multiprecision::backends::float128_backend,0> and long  
Error in lexical cast  
Source type = class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> > "-1073741823.999999999999999999999999927"  
Target type = __int64  
In function: test_negative_mixed_numeric_limits  
libs/multiprecision/test/test_arithmetic.hpp:1549 Unexpected exception of type struct boost::wrapexcept<class boost::bad_lexical_cast>  
Errot message was: bad lexical cast: source type value could not be interpreted as target  
Error in lexical cast  
Source type = class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> > "1073741823.999999999999999999999999927"  
Target type = __int64  
In function: test_mixed_numeric_limits  
libs/multiprecision/test/test_arithmetic.hpp:1921 Unexpected exception of type struct boost::wrapexcept<class boost::bad_lexical_cast>  
Errot message was: bad lexical cast: source type value could not be interpreted as target  
Testing mixed arithmetic with type: class boost::multiprecision::number<struct boost::multiprecision::backends::float128_backend,0> and unsigned long  
Error in lexical cast  
Source type = class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> > "2147483647.999999999999999999999999855"  
Target type = unsigned __int64  
In function: test_mixed_numeric_limits  
libs/multiprecision/test/test_arithmetic.hpp:1921 Unexpected exception of type struct boost::wrapexcept<class boost::bad_lexical_cast>  
Errot message was: bad lexical cast: source type value could not be interpreted as target  
Testing mixed arithmetic with type: class boost::multiprecision::number<struct boost::multiprecision::backends::float128_backend,0> and __int64  
Testing mixed arithmetic with type: class boost::multiprecision::number<struct boost::multiprecision::backends::float128_backend,0> and __int64  
Error in lexical cast  
Source type = class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> > "-4611686018427387903.999999999999999809"  
Target type = __int64  
In function: test_negative_mixed_numeric_limits  
libs/multiprecision/test/test_arithmetic.hpp:1549 Unexpected exception of type struct boost::wrapexcept<class boost::bad_lexical_cast>  
Errot message was: bad lexical cast: source type value could not be interpreted as target  
Error in lexical cast  
Source type = class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> > "4611686018427387903.999999999999999809"  
Target type = __int64  
In function: test_mixed_numeric_limits  
libs/multiprecision/test/test_arithmetic.hpp:1921 Unexpected exception of type struct boost::wrapexcept<class boost::bad_lexical_cast>  
Errot message was: bad lexical cast: source type value could not be interpreted as target  
Testing mixed arithmetic with type: class boost::multiprecision::number<struct boost::multiprecision::backends::float128_backend,0> and unsigned __int64  
Error in lexical cast  
Source type = class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> > "1023.999999999999999999999999999999941"  
Target type = unsigned __int64  
In function: test_mixed_numeric_limits  
libs/multiprecision/test/test_arithmetic.hpp:1921 Unexpected exception of type struct boost::wrapexcept<class boost::bad_lexical_cast>  
Errot message was: bad lexical cast: source type value could not be interpreted as target  
Testing mixed arithmetic with type: class boost::multiprecision::number<struct boost::multiprecision::backends::float128_backend,0> and float  
Testing mixed arithmetic with type: class boost::multiprecision::number<struct boost::multiprecision::backends::float128_backend,0> and float  
Testing mixed arithmetic with type: class boost::multiprecision::number<struct boost::multiprecision::backends::float128_backend,0> and double  
Testing mixed arithmetic with type: class boost::multiprecision::number<struct boost::multiprecision::backends::float128_backend,0> and double  
Testing mixed arithmetic with type: class boost::multiprecision::number<struct boost::multiprecision::backends::float128_backend,0> and long double  
Testing mixed arithmetic with type: class boost::multiprecision::number<struct boost::multiprecision::backends::float128_backend,0> and long double  
17 errors detected.  
```

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2022-02-01 19:19:53 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/427#issuecomment-1027200589  

OK good, those errors result from apparently buggy _Quad -> string conversion where we're getting "128.0000000000000000000000000000000031" rather than "128".  I'll try to look into that shortly.

---

## Comment 3

> Username: ckormanyos  
> Created_at: 2022-02-01 19:22:07 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/427#issuecomment-1027202480  

> apparently buggy _Quad  
  
In that particular string, you're going for more than 34 decimal digits. This is the upper range of decimal digits for the type. perhaps simply truncate the string needed?

---

## Comment 4

> Username: jzmaddock  
> Created_at: 2022-02-02 12:12:37 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/427#issuecomment-1027877398  

max_digits10 is 36, but we seem to be printing 39 unless I'm missing something, weird.

---

## Comment 5

> Username: ckormanyos  
> Created_at: 2022-02-02 12:53:22 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/427#issuecomment-1027911935  

> but we seem to be printing 39 unless I'm missing something, weird.  
  
This is just a crazy hunch here, because this kind of thing has happened to me before. But what if somewhere in the code a print function gets zero (via an unintentional non-specialized use of `numeric_limits<__float128>`) and ends up printing 39 as some kind of default?  
  
I have not yet looked into the offending code yet. i will if I get a chance...

---

## Comment 6

> Username: jzmaddock  
> Created_at: 2022-02-02 13:06:17 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/427#issuecomment-1027922352  

Just looking at this now, there was a cut-and-paste error that meant we were printing one digit too many in the "print all the digits case", but it still gets things wrong.... I need to investigate some more.

---

## Comment 7

> Username: jzmaddock  
> Created_at: 2022-02-04 19:25:46 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/427#issuecomment-1030284258  

The errors shown up in test_arithmetic_float_128.cpp should be fixed in https://github.com/boostorg/multiprecision/pull/430 which also incorporates these changes.  
  
What does NOT work is float128 <-> string round tripping as the string conversion routine does not always round correctly.  However, I'm probably going to leave that for now as it's a lot of work to fix, great shame that Intel doesn't provide their own string conversion routine here!

---

## Comment 8

> Username: jzmaddock  
> Created_at: 2022-02-06 09:18:25 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/427#issuecomment-1030783524  

I'm not sure why this hasn't closed automatically, the changes are now in develop.  Thanks for this!

---
