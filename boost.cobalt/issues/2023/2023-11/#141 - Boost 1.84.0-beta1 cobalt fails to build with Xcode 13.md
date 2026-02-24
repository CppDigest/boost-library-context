# #141 - Boost 1.84.0-beta1 cobalt fails to build with Xcode 13 [Closed]

> Username: gjasny  
> Created at: 2023-11-23 12:36:49 UTC  
> Updated at: 2024-11-13 04:02:26 UTC  
> Closed at: 2024-11-13 04:02:26 UTC  
> Url: https://github.com/boostorg/cobalt/issues/141  

Hello,  
  
With Xcode 13 the `cobalt` compilation fails:  
  
```  
[2023-11-23T10:34:45.911Z]     "clang++"   -std=c++11 -O3 -Wall -fvisibility=default -Wno-inline -std=c++20 -stdlib=libc++ -g -isysroot /Applications/Xcode-13.3.1.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX12.3.sdk -arch arm64 -mmacosx-version-min=11.0 -g -isysroot /Applications/Xcode-13.3.1.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX12.3.sdk -arch arm64 -mmacosx-version-min=11.0 -DBOOST_ALL_NO_LIB=1 -DBOOST_ASIO_NO_DEPRECATED -DBOOST_COBALT_SOURCE=1 -DBOOST_COBALT_USE_STD_PMR=1 -DNDEBUG   -I"."  -c -o "/Users/buildeng/jenkins/1wfk26brvqa8893/_output/macosx/boost/bin.v2/libs/cobalt/build/clang-darwin-13/release/address-model-32_64/cxxstd-11-iso/link-static/threading-multi/visibility-global/error.o" "libs/cobalt/src/error.cpp"  
[2023-11-23T10:34:45.911Z]   
[2023-11-23T10:34:45.911Z] ...failed clang-darwin.compile.c++ /Users/buildeng/jenkins/1wfk26brvqa8893/_output/macosx/boost/bin.v2/libs/cobalt/build/clang-darwin-13/release/address-model-32_64/cxxstd-11-iso/link-static/threading-multi/visibility-global/error.o...  
[2023-11-23T10:34:45.911Z] clang-darwin.compile.c++ /Users/buildeng/jenkins/1wfk26brvqa8893/_output/macosx/boost/bin.v2/libs/cobalt/build/clang-darwin-13/release/address-model-32_64/cxxstd-11-iso/link-static/threading-multi/visibility-global/this_thread.o  
[2023-11-23T10:34:45.911Z] In file included from libs/cobalt/src/this_thread.cpp:8:  
[2023-11-23T10:34:45.911Z] In file included from ./boost/cobalt/this_thread.hpp:11:  
[2023-11-23T10:34:45.911Z] ./boost/cobalt/config.hpp:55:10: fatal error: 'memory_resource' file not found  
[2023-11-23T10:34:45.911Z] #include <memory_resource>  
[2023-11-23T10:34:45.911Z]          ^~~~~~~~~~~~~~~~~  
[2023-11-23T10:34:45.911Z] 1 error generated.  
```  
  
Could you please add a check and only conditionally enable `cobalt`?  
  
Thanks,  
Gregor

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2023-11-27 00:40:12 UTC  
> Url: https://github.com/boostorg/cobalt/issues/141#issuecomment-1826965673  

How are you building? You got both C++11 and C++20 defined.
