# #150 - Boost 1.83.0 Beta 1 fails to build for Android API level 19 [Closed]

> Username: gjasny  
> Created at: 2023-07-13 07:49:00 UTC  
> Updated at: 2023-07-26 07:18:22 UTC  
> Closed at: 2023-07-26 07:10:00 UTC  
> Url: https://github.com/boostorg/core/issues/150  

Hello,  
  
while testing Boost 1.83.0-beta1 I noticed the following new compile error with Android `armeabi-v7a` and API level 19:  
```  
    "/opt/android-ndk-r25c/toolchains/llvm/prebuilt/linux-x86_64/bin/armv7a-linux-androideabi19-clang++"   -O3 -Wall -fvisibility=default -Wno-inline -fexceptions -fpic -frtti -std=c++20 -DBOOST_ALL_NO_LIB=1 -DBOOST_THREAD_USE_LIB=1 -DNDEBUG   -I"."  -c -o "/home/ec2-user/3xjwvqlbcmbwqe3/_output/Applications_Android_armeabi-v7a_cxx20/boost/bin.v2/libs/wave/build/clang-linux-android/release/link-static/pch-off/target-os-android/threading-multi/visibility-global/instantiate_has_include_grammar.o" "libs/wave/src/instantiate_has_include_grammar.cpp"  
  
...failed clang-linux.compile.c++ /home/ec2-user/3xjwvqlbcmbwqe3/_output/Applications_Android_armeabi-v7a_cxx20/boost/bin.v2/libs/wave/build/clang-linux-android/release/link-static/pch-off/target-os-android/threading-multi/visibility-global/instantiate_has_include_grammar.o...  
clang-linux.compile.c++ /home/ec2-user/3xjwvqlbcmbwqe3/_output/Applications_Android_armeabi-v7a_cxx20/boost/bin.v2/libs/wave/build/clang-linux-android/release/link-static/pch-off/target-os-android/threading-multi/visibility-global/instantiate_cpp_exprgrammar.o  
In file included from libs/wave/src/instantiate_cpp_exprgrammar.cpp:24:  
In file included from ./boost/wave/grammars/cpp_expression_grammar.hpp:17:  
In file included from ./boost/spirit/include/classic_core.hpp:11:  
In file included from ./boost/spirit/home/classic/core.hpp:42:  
In file included from ./boost/spirit/home/classic/core/non_terminal/grammar.hpp:21:  
In file included from ./boost/spirit/home/classic/core/non_terminal/impl/grammar.ipp:15:  
In file included from ./boost/spirit/home/classic/core/non_terminal/impl/object_with_id.ipp:14:  
In file included from ./boost/shared_ptr.hpp:17:  
In file included from ./boost/smart_ptr/shared_ptr.hpp:30:  
In file included from ./boost/smart_ptr/detail/spinlock_pool.hpp:25:  
In file included from ./boost/smart_ptr/detail/spinlock.hpp:42:  
In file included from ./boost/smart_ptr/detail/spinlock_gcc_atomic.hpp:14:  
In file included from ./boost/smart_ptr/detail/yield_k.hpp:22:  
In file included from ./boost/core/yield_primitives.hpp:10:  
./boost/core/detail/sp_thread_sleep.hpp:73:29: error: use of undeclared identifier 'PTHREAD_CANCEL_DISABLE'  
    pthread_setcancelstate( PTHREAD_CANCEL_DISABLE, &oldst );  
                            ^  
./boost/core/detail/sp_thread_sleep.hpp:90:5: error: use of undeclared identifier 'pthread_setcancelstate'  
    pthread_setcancelstate( oldst, &oldst );  
    ^  
2 errors generated.  
```  
  
I could not find any reference to `pthread_setcancelstate` in Android NDK r25c, so I guess the Android platform does not implement `pthread_setcancelstate`.  
  
This seemed to be introduced by @pdimov in 23ef6d35316be0dfc4e110e701f2fccdeae94eec.  
  
Thanks,  
Gregor

---

## Comment 1

> Username: gjasny  
> Created at: 2023-07-13 07:50:12 UTC  
> Url: https://github.com/boostorg/core/issues/150#issuecomment-1633742589  

CC: @mclow

---

## Comment 2

> Username: pdimov  
> Created at: 2023-07-13 22:13:26 UTC  
> Url: https://github.com/boostorg/core/issues/150#issuecomment-1634985634  

Looks like we need to add a check for Android [here](https://github.com/boostorg/core/blob/216999e552e7f73e63c7bcc88b8ce9c179bbdbe2/include/boost/core/detail/sp_thread_sleep.hpp#L70) and [here](https://github.com/boostorg/core/blob/216999e552e7f73e63c7bcc88b8ce9c179bbdbe2/include/boost/core/detail/sp_thread_sleep.hpp#L88).

---

## Comment 3

> Username: pdimov  
> Created at: 2023-07-25 15:04:31 UTC  
> Url: https://github.com/boostorg/core/issues/150#issuecomment-1650017547  

Can you please check whether https://github.com/boostorg/core/commit/c7134904e27237dbbb81ba28f0f23fc365b079dd fixes the issue?

---

## Comment 4

> Username: gjasny  
> Created at: 2023-07-25 19:10:39 UTC  
> Url: https://github.com/boostorg/core/issues/150#issuecomment-1650388105  

> Can you please check whether [c713490](https://github.com/boostorg/core/commit/c7134904e27237dbbb81ba28f0f23fc365b079dd) fixes the issue?  
  
Thanks for the fix. I’m traveling right now an won’t be able to verify in time for the next boost release.

---

## Comment 5

> Username: pdimov  
> Created at: 2023-07-25 19:14:49 UTC  
> Url: https://github.com/boostorg/core/issues/150#issuecomment-1650394230  

I can't verify it either so the best I can do is apply it and hope for the best. :-)

---

## Comment 6

> Username: MarcelRaad  
> Created at: 2023-07-26 07:18:22 UTC  
> Url: https://github.com/boostorg/core/issues/150#issuecomment-1651118641  

Hi, I verified that it fixes the build of Boost.Test and Boost.Thread for Android. Thanks!
