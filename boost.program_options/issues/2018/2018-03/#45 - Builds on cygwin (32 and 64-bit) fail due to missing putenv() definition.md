# #45 - Builds on cygwin (32 and 64-bit) fail due to missing putenv() definition [Closed]

> Username: jeking3  
> Created at: 2018-03-07 13:01:52 UTC  
> Updated at: 2024-04-08 03:01:25 UTC  
> Closed at: 2024-04-08 03:01:25 UTC  
> Url: https://github.com/boostorg/program_options/issues/45  

Likely a missing include or macro in the build environment.  Identified with new CI build suite.  
See: https://ci.appveyor.com/project/jeking3/program-options/build/job/6cwpgdbjt75iw334  
  
```  
libs\program_options\test\parsers_test.cpp: In function 'void test_environment()':  
674libs\program_options\test\parsers_test.cpp:255:46: error: 'putenv' was not declared in this scope  
675     putenv(const_cast<char*>("PO_TEST_FOO=1"));  
676                                              ^  
677  
678    "g++"   -std=c++03 -m64 -O0 -fno-inline -Wall -g  -DBOOST_ALL_NO_LIB=1  -I"." -c -o "bin.v2\libs\program_options\test\parsers_test.test\gcc-6.4.0\debug\cxxstd-03-iso\link-static\target-os-cygwin\parsers_test.o" "libs\program_options\test\parsers_test.cpp"  
679  
680...failed gcc.compile.c++ bin.v2\libs\program_options\test\parsers_test.test\gcc-6.4.0\debug\cxxstd-03-iso\link-static\target-os-cygwin\parsers_test.o...  
```

---

## Comment 1

> Username: teeks99  
> Created at: 2021-08-29 14:13:28 UTC  
> Url: https://github.com/boostorg/program_options/issues/45#issuecomment-907798489  

I see that CI was added for appoveyor for cygwin and mingw but they don't seem to be passing. Was the point of that merge to show this bug?

---

## Comment 2

> Username: vprus  
> Created at: 2021-08-29 20:16:33 UTC  
> Url: https://github.com/boostorg/program_options/issues/45#issuecomment-907864969  

Thanks for the report! Unfortunately, the last time I used Cygwin was about 20 years ago, so I won't be able to fix this problem - but will appreciate patches.

---

## Comment 3

> Username: pdimov  
> Created at: 2024-04-08 03:01:25 UTC  
> Url: https://github.com/boostorg/program_options/issues/45#issuecomment-2041776885  

Fixed in https://github.com/boostorg/program_options/commit/d1ddaa8a0eca167db6669f42ff57f478500ca323.
