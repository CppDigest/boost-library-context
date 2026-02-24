# #100 - ambiguous constructor error with clang7 from ndk [Closed]

> Username: DDoSolitary  
> Created at: 2022-12-27 06:47:25 UTC  
> Updated at: 2023-01-10 02:03:50 UTC  
> Closed at: 2023-01-10 02:03:50 UTC  
> Url: https://github.com/boostorg/system/issues/100  

I'm trying to build boost 1.80 with the Clang 7 compiler provided by NDK r18b and got the following error  
  
```  
In file included from libs/thread/src/future.cpp:10:  
In file included from ./boost/thread/futures/future_error_code.hpp:13:  
In file included from ./boost/system/error_code.hpp:13:  
./boost/system/detail/error_code.hpp:615:29: error: call to constructor of 'std::error_code' is ambiguous  
            std::error_code e2( *this );  
                            ^   ~~~~~  
/opt/android-toolchain/bin/../lib/gcc/arm-linux-androideabi/4.9.x/../../../../include/c++/4.9.x/system_error:500:24: note: candidate is the implicit copy constructor  
class _LIBCPP_TYPE_VIS error_code  
                       ^  
/opt/android-toolchain/bin/../lib/gcc/arm-linux-androideabi/4.9.x/../../../../include/c++/4.9.x/system_error:500:24: note: candidate is the implicit move constructor  
1 error generated.  
```  
  
It seems to be the same error for which a special deleted declaration was added a6c4b6329c3b8e3d131d007b87ac691dd65dc85a. But the check fails to catch my clang version.  
  
```  
$ /opt/android-toolchain/bin/aarch64-linux-android-clang++ --version  
Android (4751641 based on r328903) clang version 7.0.2 (https://android.googlesource.com/toolchain/clang 003100370607242ddd5815e4a043907ea9004281) (https://android.googlesource.com/toolchain/llvm 1d739ffb0366421d383e04ff80ec2ee591315116) (based on LLVM 7.0.2svn)  
Target: aarch64-none-linux-android21  
Thread model: posix  
InstalledDir: /opt/android-toolchain/bin  
```

---

## Comment 1

> Username: pdimov  
> Created at: 2022-12-27 12:09:18 UTC  
> Url: https://github.com/boostorg/system/issues/100#issuecomment-1365857706  

It looks like the same issue, yes. Does it work if you define the macro `BOOST_SYSTEM_CLANG_6`, which is defined for Clang 6 and earlier by https://github.com/boostorg/system/blob/36e1236a0f025703def959ae7126e255f968b9b7/include/boost/system/detail/config.hpp#L68?

---

## Comment 2

> Username: DDoSolitary  
> Created at: 2023-01-09 02:41:25 UTC  
> Url: https://github.com/boostorg/system/issues/100#issuecomment-1375039009  

@pdimov Yes, manually defining the macro fixes the error.

---

## Comment 3

> Username: pdimov  
> Created at: 2023-01-09 03:00:07 UTC  
> Url: https://github.com/boostorg/system/issues/100#issuecomment-1375044641  

The normal Clang 7.0.0, 7.0.1 works, so this Android build should be something slightly different that identifies as 7.0.2 but isn't. Any suggestions as to how the condition in `detail/config.hpp` needs to be changed to fix your issue?  
```  
#if defined(__clang__) && (__clang_major__ < 7 || (defined(__APPLE__) && __clang_major__ < 11) || (defined(__ANDROID__) && __clang_major__ == 7))  
```  
perhaps?

---

## Comment 4

> Username: DDoSolitary  
> Created at: 2023-01-09 03:02:59 UTC  
> Updated at: 2023-01-09 03:03:47 UTC  
> Url: https://github.com/boostorg/system/issues/100#issuecomment-1375046808  

I also tested normal clang 7.0.0 and can confirm it works. I think adding the \_\_ANDROID\_\_ && clang == 7 check should fix the error for me, though I didn't test any newer clang versions provided by Android NDK

---

## Comment 5

> Username: pdimov  
> Created at: 2023-01-10 02:03:20 UTC  
> Url: https://github.com/boostorg/system/issues/100#issuecomment-1376620897  

OK, let's give it a try.
