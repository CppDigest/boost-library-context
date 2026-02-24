# #89 - Build fails on clang-5 with libstdc++7-dev (C++17 issue) [Closed]

> Username: jeking3  
> Created at: 2018-11-13 13:10:17 UTC  
> Updated at: 2018-11-14 12:52:40 UTC  
> Closed at: 2018-11-14 12:52:40 UTC  
> Url: https://github.com/boostorg/container/issues/89  

Setup:  
```  
  clang-5:  &clang-5  { apt: { packages: [ "clang-5.0",  
                                           "libstdc++-7-dev" ], sources: [ "llvm-toolchain-trusty-5.0",  
                                                                           "ubuntu-toolchain-r-test"   ] } }  
```  
  
Command:  
```  
/home/travis/build/jeking3/boost-root/b2 . toolset=clang-5.0 cxxstd=03,11,14,17 variant=release,debug -j3  
```  
  
Output:  
  
https://travis-ci.org/jeking3/container/jobs/454295432#L1582  
```  
clang-linux.compile.c++.without-pth ../../bin.v2/libs/container/test/set_test.test/clang-linux-5.0/release/cxxstd-17-iso/visibility-hidden/set_test.o  
../../libs/container/test/set_test.cpp:622:19: error: no viable constructor or deduction guide for deduction of template arguments of 'multiset'  
      auto gold = std::multiset({ 1, 2, 3 });  
                  ^  
...  
1 error generated.  
  "clang++" -c -x c++ -std=c++17 -fvisibility-inlines-hidden -fPIC -m64 -O3 -Wall -fvisibility=hidden -Wno-inline  -DBOOST_ALL_NO_LIB=1 -DBOOST_CONTAINER_DYN_LINK=1 -DNDEBUG -I"../.." -o "../../bin.v2/libs/container/test/set_test.test/clang-linux-5.0/release/cxxstd-17-iso/visibility-hidden/set_test.o" "../../libs/container/test/set_test.cpp"  
...failed clang-linux.compile.c++.without-pth ../../bin.v2/libs/container/test/set_test.test/clang-linux-5.0/release/cxxstd-17-iso/visibility-hidden/set_test.o...  
```

---

## Comment 1

> Username: jeking3  
> Created at: 2018-11-14 12:52:40 UTC  
> Url: https://github.com/boostorg/container/issues/89#issuecomment-438651522  

This is now resolved.  
  
https://travis-ci.org/jeking3/container/builds/454804441
