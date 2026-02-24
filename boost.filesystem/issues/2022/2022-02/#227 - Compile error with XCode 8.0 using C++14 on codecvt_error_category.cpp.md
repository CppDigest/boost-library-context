# #227 - Compile error with XCode 8.0 using C++14 on codecvt_error_category.cpp [Closed]

> Username: jeremy-murphy  
> Created at: 2022-02-07 23:23:27 UTC  
> Updated at: 2022-02-08 21:05:20 UTC  
> Closed at: 2022-02-08 12:20:54 UTC  
> Url: https://github.com/boostorg/filesystem/issues/227  

This error just popped up in the BGL Drone CI.  
```  
clang-darwin.compile.c++ ../../../bin.v2/libs/filesystem/build/clang-darwin-8/debug/cxxstd-14-iso/threading-multi/visibility-hidden/codecvt_error_category.o  
../../../libs/filesystem/src/codecvt_error_category.cpp:79:36: error: default initialization of an object of const type 'const boost::filesystem::(anonymous namespace)::codecvt_error_cat' without a user-provided default constructor  
    static const codecvt_error_cat codecvt_error_cat_const;  
                                   ^  
                                                          {}  
1 error generated.  
  
```  
https://drone.cpp.al/boostorg/graph/75/100/2  
https://github.com/boostorg/graph/pull/269

---

## Comment 1

> Username: Lastique  
> Created at: 2022-02-08 12:24:25 UTC  
> Url: https://github.com/boostorg/filesystem/issues/227#issuecomment-1032552229  

Thanks for the report.

---

## Comment 2

> Username: jeremy-murphy  
> Created at: 2022-02-08 21:05:20 UTC  
> Url: https://github.com/boostorg/filesystem/issues/227#issuecomment-1033061093  

Thanks for the rapid support!
