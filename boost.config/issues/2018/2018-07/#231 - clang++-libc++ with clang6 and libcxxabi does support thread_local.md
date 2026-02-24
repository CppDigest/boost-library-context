# #231 - clang++-libc++ with clang6 and libcxxabi does support thread_local [Closed]

> Username: KategoryBee  
> Created at: 2018-07-25 23:55:15 UTC  
> Updated at: 2018-08-06 11:14:12 UTC  
> Closed at: 2018-08-06 11:14:12 UTC  
> Url: https://github.com/boostorg/config/issues/231  

I've been upgrading some company internal projects to use a clang6 toolchain, and discovered that the fix in #208 is causing boost::fiber to not build since it thinks `thread_local` isn't available.  
  
I can successfully build and run the test case in `boost_no_cxx11_thread_local.ipp` when i import it in to the project (we're using cmake). This is with the compiler flags:  
  
```  
cxx:  
-stdlib=libc++ -fPIE -pthread -std=c++1z  
  
link:  
-stdlib=libc++ -static-libstdc++ -Wl,-Bstatic -lc++abi -Wl,-Bdynamic -pthread   
```  
  
Checking the symbols against the debian 8 prebuilts from http://releases.llvm.org/  
  
```  
clang 6:  
> nm -C libc++abi.so | grep __cxa_thread  
0000000000027350 T __cxa_thread_atexit  
                 U __cxa_thread_atexit_impl@@GLIBC_2.18  
  
clang 5:  
> nm -C libc++abi.so | grep __cxa_thread  
000000000003c020 T __cxa_thread_atexit  
                 U __cxa_thread_atexit_impl@@GLIBC_2.18  
  
clang 3.4 on ubuntu trusty, via apt-get install clang libc++-dev libc++abi-dev:  
> nm -CD ./usr/lib/x86_64-linux-gnu/libc++abi.so | grep __cxa_thread  
<no results>  
```  
  
So it seems that clang3's libc++abi didn't have support for `__cxa_thread_atexit`, but newer versions do by deferring to glibc's implementation.  
  
The feature check should probably be changed to detect the clang version?   
  
This may not work 100% of the time though, since it depends on what standard library is being linked in and if libcxxabi is being linked in. Based on the commit https://github.com/llvm-mirror/libcxxabi/commit/3f7b3700184b818c28ee085e3d629aa5f314169b more recent versions of clang will attempt to provide a fallback in libcxxabi if glibc does not have an implementation, so perhaps require a very recent version of clang?

---

## Comment 1

> Username: KategoryBee  
> Created at: 2018-07-26 00:32:47 UTC  
> Url: https://github.com/boostorg/config/issues/231#issuecomment-407938550  

Did some digging and that commit to libcxxabi looks like it was included in clang4. Here's the patch I'm currently using. It might break some users that aren't using libcxxabi though.. not really too sure on the best way to handle this.  
  
```patch  
--- boost/config/stdlib/libcpp.hpp	2018-07-26 10:09:30.091065048 +1000  
+++ boost/config/stdlib/libcpp.hpp	2018-07-26 10:14:24.641990321 +1000  
@@ -113,10 +113,16 @@  
 #  define BOOST_NO_CXX11_THREAD_LOCAL  
 #endif  
   
-#if defined(__linux__) && !defined(BOOST_NO_CXX11_THREAD_LOCAL)  
+#if defined(__linux__) && _LIBCPP_VERSION < 4000 && !defined(BOOST_NO_CXX11_THREAD_LOCAL)  
 // After libc++-dev is installed on Trusty, clang++-libc++ almost works,  
 // except uses of `thread_local` fail with undefined reference to  
 // `__cxa_thread_atexit`.  
+//  
+// clang's libc++abi provides an implementation by deferring to the glibc  
+// implementation, which may or may not be available (it is not on Trusty).  
+// clang 4's libc++abi will provide an implementation if one is not in glibc  
+// though, so thread local support should work with clang 4 and above as long  
+// as libc++abi is linked in.  
 #  define BOOST_NO_CXX11_THREAD_LOCAL  
 #endif  
   
  
```

---

## Comment 2

> Username: jzmaddock  
> Created at: 2018-08-04 07:26:54 UTC  
> Url: https://github.com/boostorg/config/issues/231#issuecomment-410430575  

Confirmed as working with Ubuntu clang-6 packages at least.

---

## Comment 3

> Username: jzmaddock  
> Created at: 2018-08-04 13:53:30 UTC  
> Url: https://github.com/boostorg/config/issues/231#issuecomment-410451040  

Spoke too soon.... failed CI testing with clang-5.  Updating the CI tests and waiting for them to cycle...

---

## Comment 4

> Username: jzmaddock  
> Created at: 2018-08-06 11:14:12 UTC  
> Url: https://github.com/boostorg/config/issues/231#issuecomment-410674396  

CI is passing now, not sure if we have all the wrinkles sorted, but it's better than it was.
