# #276 Fix Issue 275 [Merged]

> Username: austin-beer  
> Created at: 2019-04-01 16:25:45 UTC  
> Updated at: 2019-04-03 17:26:58 UTC  
> Merged at: 2019-04-02 17:02:26 UTC  
> Closed at: 2019-04-02 17:02:26 UTC  
> Url: https://github.com/boostorg/thread/pull/276  

* Re-fixed the EINTR bug that exists in some pthreads implementations.  
It was originally fixed in https://svn.boost.org/trac10/ticket/6200 and  
was accidentally disabled in 5b209c2e834a89c93d308aa7ddb1dfd1207d929d.  
* Made sure that the fix for the EINTR bug was consistently applied to  
all code in the library.  
* Made sure that all pthread_mutex_*() and pthread_cond_*() function  
calls in the library were consistently decorated with  
BOOST_THREAD_DISABLE_THREAD_SAFETY_ANALYSIS.

---

## Review 1 [Commented]

> Username: Kojoley  
> Created_at: 2019-04-02 22:07:03 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/thread/pull/276#pullrequestreview-221925948  

📁 include/boost/thread/pthread/condition_variable_fwd.hpp

```diff
  89 |-             } while (ret == EINTR);
  85 |+             ret = posix::pthread_cond_destroy(&cond);
  86 |             BOOST_ASSERT(!ret);
```

> Username: Kojoley  
> Created_at: 2019-04-02 22:07:02 UTC  
> Updated_at: 2019-04-02 22:07:03 UTC  
> Url: https://github.com/boostorg/thread/pull/276#discussion_r271514388  

Please change both `BOOST_ASSERT` into `BOOST_VERIFY` if everything is ok with the logic itself.  
  
```  
gcc.compile.c++ ../../../../bin.v2/libs/spirit/classic/test/grammar_mt_tests.test/gcc-8/release/cxxstd-03-iso/threadapi-pthread/threading-multi/visibility-hidden/grammar_mt_tests.o  
In file included from ../../../../boost/thread/pthread/thread_data.hpp:14,  
                 from ../../../../boost/thread/thread_only.hpp:17,  
                 from ../../../../boost/thread/thread.hpp:12,  
                 from grammar_mt_tests.cpp:33:  
../../../../boost/thread/pthread/condition_variable_fwd.hpp: In destructor ‘boost::condition_variable::~condition_variable()’:  
../../../../boost/thread/pthread/condition_variable_fwd.hpp:79:17: error: variable ‘ret’ set but not used [-Werror=unused-but-set-variable]  
             int ret;  
                 ^~~  
cc1plus: all warnings being treated as errors  
    "ccache" "g++-8"   -std=c++03 -fvisibility-inlines-hidden -fPIC -m64 -pthread -O3 -finline-functions -Wno-inline -Wall -Wextra -Werror -fvisibility=hidden -Wno-maybe-uninitialized -Wno-sign-compare -Wno-implicit-fallthrough  -DBOOST_ALL_NO_LIB -DBOOST_ALL_NO_LIB=1 -DBOOST_CHRONO_DYN_LINK=1 -DBOOST_THREAD_BUILD_DLL=1 -DBOOST_THREAD_POSIX -DBOOST_THREAD_USE_DLL=1 -DNDEBUG  -I"." -I"../../../.." -c -o "../../../../bin.v2/libs/spirit/classic/test/grammar_mt_tests.test/gcc-8/release/cxxstd-03-iso/threadapi-pthread/threading-multi/visibility-hidden/grammar_mt_tests.o" "grammar_mt_tests.cpp"  
```

> Username: austin-beer  
> Created_at: 2019-04-03 15:39:03 UTC  
> Url: https://github.com/boostorg/thread/pull/276#discussion_r271805590  

Ok, I'll submit another PR for this.

> Username: austin-beer  
> Created_at: 2019-04-03 17:26:58 UTC  
> Url: https://github.com/boostorg/thread/pull/276#discussion_r271850669  

#278


---
