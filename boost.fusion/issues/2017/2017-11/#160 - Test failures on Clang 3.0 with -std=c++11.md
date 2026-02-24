# #160 - Test failures on Clang 3.0 with -std=c++11 [Closed]

> Username: Kojoley  
> Created at: 2017-11-15 19:45:56 UTC  
> Updated at: 2017-12-24 02:40:58 UTC  
> Closed at: 2017-12-24 02:40:58 UTC  
> Url: https://github.com/boostorg/fusion/issues/160  

e.g. http://www.boost.org/development/tests/develop/developer/output/teeks99-02-dc3-0-11-Docker-64on64-boost-bin-v2-libs-fusion-test-adapt_adt-test-clang-gnu-linux-3-0~c++11-debug-threadapi-pthread.html  
```  
  "clang++-3.0" -c -x c++ -Wno-c99-extensions -std=c++11 -O0 -g -fno-inline -Wall -g -fPIC -m64  -DBOOST_ALL_NO_LIB=1 -I".." -o "/var/boost/run/results/boost/bin.v2/libs/fusion/test/adapt_adt.test/clang-gnu-linux-3.0~c++11/debug/threadapi-pthread/sequence/adapt_adt.o" "../libs/fusion/test/sequence/adapt_adt.cpp"  
  
In file included from ../libs/fusion/test/sequence/adapt_adt.cpp:16:  
../boost/fusion/container/vector/vector.hpp:250:61: error: invalid use of 'this' outside of a nonstatic member function  
            auto at_impl(J) -> decltype(at_detail<J::value>(this))  
                                                            ^  
../boost/fusion/container/vector/vector.hpp:251:13: error: type name requires a specifier or qualifier  
            {  
            ^  
../boost/fusion/container/vector/vector.hpp:251:13: error: C++ requires a type specifier for all declarations  
            {  
            ^  
../boost/fusion/container/vector/vector.hpp:250:13: error: 'auto' return without trailing return type  
            auto at_impl(J) -> decltype(at_detail<J::value>(this))  
            ^  
../boost/fusion/container/vector/vector.hpp:257:67: error: invalid use of 'this' outside of a nonstatic member function  
            auto at_impl(J) const -> decltype(at_detail<J::value>(this))  
                                                                  ^  
../boost/fusion/container/vector/vector.hpp:258:13: error: type name requires a specifier or qualifier  
            {  
            ^  
../boost/fusion/container/vector/vector.hpp:258:13: error: C++ requires a type specifier for all declarations  
            {  
            ^  
../boost/fusion/container/vector/vector.hpp:257:13: error: 'auto' return without trailing return type  
            auto at_impl(J) const -> decltype(at_detail<J::value>(this))  
            ^  
```
