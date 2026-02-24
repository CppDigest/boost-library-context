# #79 - Regression on powerpc-darwin build fails with `Parameter syntax error (parameter 1)` [Closed]

> Username: barracuda156  
> Created at: 2025-12-13 13:23:10 UTC  
> Updated at: 2025-12-13 16:04:05 UTC  
> Closed at: 2025-12-13 16:04:05 UTC  
> Url: https://github.com/boostorg/atomic/issues/79  

1.90.0 fails to build on powerpc:  
```  
:info:build darwin.compile.c++ bin.v2/libs/atomic/build/darwin-14.3.0/release/power_32/threading-multi/visibility-hidden/lock_pool.o  
:info:build     "/opt/local/bin/g++-mp-14"   -fvisibility-inlines-hidden -Os -std=gnu++17 -D_GLIBCXX_USE_CXX11_ABI=0 -arch ppc  -fPIC -m32 -O3 -Wall -fvisibility=hidden -dynamic -gdwarf-2 -fexceptions -Wno-inline  -DBOOST_ALL_NO_LIB=1 -DBOOST_ATOMIC_DYN_LINK=1 -DBOOST_ATOMIC_SOURCE -DNDEBUG   -I"." -I"/opt/local/var/macports/build/boost190-bfd9cba2/work/boost_1_90_0/libs/assert/include" -I"/opt/local/var/macports/build/boost190-bfd9cba2/work/boost_1_90_0/libs/atomic/include" -I"/opt/local/var/macports/build/boost190-bfd9cba2/work/boost_1_90_0/libs/atomic/src" -I"/opt/local/var/macports/build/boost190-bfd9cba2/work/boost_1_90_0/libs/predef/include" -I"/opt/local/var/macports/build/boost190-bfd9cba2/work/boost_1_90_0/libs/preprocessor/include" -I"/opt/local/var/macports/build/boost190-bfd9cba2/work/boost_1_90_0/libs/static_assert/include" -I"/opt/local/var/macports/build/boost190-bfd9cba2/work/boost_1_90_0/libs/type_traits/include" -I"libs/config/include"  -c -o "bin.v2/libs/atomic/build/darwin-14.3.0/release/power_32/threading-multi/visibility-hidden/lock_pool.o" "/opt/local/var/macports/build/boost190-bfd9cba2/work/boost_1_90_0/libs/atomic/src/lock_pool.cpp"  
:info:build /opt/local/var/macports/build/boost190-bfd9cba2/work/.tmp/ccajY2yC.s:238:Parameter syntax error (parameter 1)  
:info:build /opt/local/var/macports/build/boost190-bfd9cba2/work/.tmp/ccajY2yC.s:259:Parameter syntax error (parameter 1)  
:info:build /opt/local/var/macports/build/boost190-bfd9cba2/work/.tmp/ccajY2yC.s:281:Parameter syntax error (parameter 1)  
:info:build /opt/local/var/macports/build/boost190-bfd9cba2/work/.tmp/ccajY2yC.s:470:Parameter syntax error (parameter 1)  
:info:build /opt/local/var/macports/build/boost190-bfd9cba2/work/.tmp/ccajY2yC.s:491:Parameter syntax error (parameter 1)  
:info:build /opt/local/var/macports/build/boost190-bfd9cba2/work/.tmp/ccajY2yC.s:513:Parameter syntax error (parameter 1)  
:info:build /opt/local/var/macports/build/boost190-bfd9cba2/work/.tmp/ccajY2yC.s:580:Parameter syntax error (parameter 1)  
:info:build /opt/local/var/macports/build/boost190-bfd9cba2/work/.tmp/ccajY2yC.s:589:Parameter syntax error (parameter 1)  
:info:build /opt/local/var/macports/build/boost190-bfd9cba2/work/.tmp/ccajY2yC.s:597:Parameter syntax error (parameter 1)  
:info:build /opt/local/var/macports/build/boost190-bfd9cba2/work/.tmp/ccajY2yC.s:1966:Parameter syntax error (parameter 1)  
:info:build /opt/local/var/macports/build/boost190-bfd9cba2/work/.tmp/ccajY2yC.s:1988:Parameter syntax error (parameter 1)  
:info:build /opt/local/var/macports/build/boost190-bfd9cba2/work/.tmp/ccajY2yC.s:2010:Parameter syntax error (parameter 1)  
:info:build /opt/local/var/macports/build/boost190-bfd9cba2/work/.tmp/ccajY2yC.s:2538:Parameter syntax error (parameter 1)  
:info:build /opt/local/var/macports/build/boost190-bfd9cba2/work/.tmp/ccajY2yC.s:2559:Parameter syntax error (parameter 1)  
:info:build /opt/local/var/macports/build/boost190-bfd9cba2/work/.tmp/ccajY2yC.s:2581:Parameter syntax error (parameter 1)  
:info:build /opt/local/var/macports/build/boost190-bfd9cba2/work/.tmp/ccajY2yC.s:2876:Parameter syntax error (parameter 1)  
:info:build /opt/local/var/macports/build/boost190-bfd9cba2/work/.tmp/ccajY2yC.s:2898:Parameter syntax error (parameter 1)  
:info:build /opt/local/var/macports/build/boost190-bfd9cba2/work/.tmp/ccajY2yC.s:2920:Parameter syntax error (parameter 1)  
:info:build ...failed darwin.compile.c++ bin.v2/libs/atomic/build/darwin-14.3.0/release/power_32/threading-multi/visibility-hidden/lock_pool.o...  
```  
  
Looks like assembler syntax is wrong somewhere. The last version known to build was 1.88 (I did not try 1.89).

---

## Comment 1

> Username: barracuda156  
> Created at: 2025-12-13 13:29:03 UTC  
> Url: https://github.com/boostorg/atomic/issues/79#issuecomment-3649414951  

Breakage introduced by https://github.com/boostorg/atomic/commit/d3940538d3d9e132ed2b09e88d36597ea8178390  
  
I will fix that.
