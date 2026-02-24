# #139 - Fiber fails to build on macOS with 1.65 rc1 [Closed]

> Username: gjasny  
> Created at: 2017-08-09 07:30:04 UTC  
> Updated at: 2017-08-09 08:48:49 UTC  
> Closed at: 2017-08-09 08:33:45 UTC  
> Url: https://github.com/boostorg/fiber/issues/139  

This is happens with   
* Boost 1.65 rc1  
* Xcode 8.3.3  
* Apple LLVM version 8.1.0 (clang-802.0.42)  
  
```  
libs/fiber/src/numa/pin_thread.cpp:25:10: warning: pin_thread() not supported [-W#pragma-messages]  
# pragma message "pin_thread() not supported"  
         ^  
libs/fiber/src/numa/pin_thread.cpp:30:22: error: expected ';' after expression  
    throw fiber_error{  
                     ^  
                     ;  
libs/fiber/src/numa/pin_thread.cpp:30:11: error: use of undeclared identifier 'fiber_error'  
    throw fiber_error{  
          ^  
libs/fiber/src/numa/pin_thread.cpp:32:54: error: expected ';' after expression  
            "boost fiber: pin_thread() not supported" };  
                                                     ^  
                                                     ;  
libs/fiber/src/numa/pin_thread.cpp:32:13: warning: expression result unused [-Wunused-value]  
            "boost fiber: pin_thread() not supported" };  
            ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
2 warnings and 3 errors generated.  
  
    "clang++" -x c++ -std=c++14 -stdlib=libc++ -mmacosx-version-min=10.8 -g -isysroot /Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX10.12.sdk -arch i386 -arch x86_64 -O3 -O3 -Wno-inline -Wall -DBOOST_ALL_NO_LIB=1 -DBOOST_DISABLE_ASSERTS -DBOOST_FIBERS_SOURCE -DBOOST_FILESYSTEM_STATIC_LINK=1 -DBOOST_SYSTEM_STATIC_LINK=1 -DNDEBUG -I"." -c -o "/Users/gjasny/Git/ExternalLibs/boost/_output/macosx/boost/bin.v2/libs/fiber/build/clang-darwin-4.2.1/release/address-model-32_64/link-static/threading-multi/numa/pin_thread.o" "libs/fiber/src/numa/pin_thread.cpp"  
```

---

## Comment 1

> Username: gjasny  
> Created at: 2017-08-09 08:27:00 UTC  
> Url: https://github.com/boostorg/fiber/issues/139#issuecomment-321189134  

See #140

---

## Comment 2

> Username: olk  
> Created at: 2017-08-09 08:33:45 UTC  
> Url: https://github.com/boostorg/fiber/issues/139#issuecomment-321190715  

ty, should be fixed be your pull request

---

## Comment 3

> Username: gjasny  
> Created at: 2017-08-09 08:43:59 UTC  
> Url: https://github.com/boostorg/fiber/issues/139#issuecomment-321193162  

Don't you also have to put it onto master?

---

## Comment 4

> Username: olk  
> Created at: 2017-08-09 08:48:49 UTC  
> Url: https://github.com/boostorg/fiber/issues/139#issuecomment-321194253  

branch master is mentioned as 'Boost 1.65.0 completely closed'
