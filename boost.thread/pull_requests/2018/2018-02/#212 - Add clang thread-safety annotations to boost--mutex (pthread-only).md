# #212 Add clang thread-safety annotations to boost::mutex (pthread-only) [Merged]

> Username: thughes  
> Created at: 2018-02-27 17:55:18 UTC  
> Updated at: 2018-09-10 21:54:09 UTC  
> Merged at: 2018-04-25 17:13:05 UTC  
> Closed at: 2018-04-25 17:13:05 UTC  
> Url: https://github.com/boostorg/thread/pull/212  

This is a first-pass attempt to get feedback for adding thread safety annotations (#101). There are more annotations that can be added to other classes, but I want to make sure that the approach and style are correct before doing more.  
  
I think the first step is to add the annotations to the associated boost classes so that downstream projects can use them. A next and more advanced step would be to get the `boost::thread` library itself compiling with analysis enabled (`-Wthread-safety`).  
  
Specifically, I'm not very familiar with boost build, so there may be better ways of doing what I'm trying to do, which is to conditionally compile tests only if the compiler is `clang`.  
  
It's also unclear to me which builds on travis need to succeed in order for the PR to be accepted since several variants are currently failing: https://travis-ci.org/boostorg/thread

---

## Comment 1

> Username: viboes  
> Created_at: 2018-02-27 20:27:54 UTC  
> Url: https://github.com/boostorg/thread/pull/212#issuecomment-369015146  

I like it.  
  
You will need to prefix all the macros with BOOST_THREAD_ to avoid possible clashes with other macros.  
  
Since which version of clang is this available?  
  
I'm not a Bjam expert neither. I find your way satisfactory. Take a look at `thread-run2-noit-pthread`, but the `<toolset>` is something I never mastered.   
  
Concerning travis, everything is ok in Linux, but MacOs is failing for some timing issues. I suspect this is a VM issue.  
  
Other concerns with your PR  
  
```  
C:/projects/boost-root/tools/build/src/build\feature.jam:491: in validate-value-string from module feature  
error: "clang" is not a known value of feature <toolset>  
error: legal values: "msvc"  
C:/projects/boost-root/tools/build/src/build\feature.jam:365: in expand-subfeatures-aux from module feature  
C:/projects/boost-root/tools/build/src/build\feature.jam:422: in expand-subfeatures from module feature  
```  
  
Maybe you need to check before `<threadapi>win32`.  
  
The next Boost 1.67 release is quite close. I would prefer to merge this for Boost-1.68.

---

## Comment 2

> Username: viboes  
> Created_at: 2018-02-27 21:36:55 UTC  
> Url: https://github.com/boostorg/thread/pull/212#issuecomment-369034771  

There is also an error with travis  
```  
/home/travis/build/boostorg/boost-root/tools/build/src/build/feature.jam:491: in validate-value-string from module feature  
error: "clang" is not a known value of feature <toolset>  
error: legal values: "gcc"  
```

---

## Comment 3

> Username: viboes  
> Created_at: 2018-02-27 22:02:21 UTC  
> Url: https://github.com/boostorg/thread/pull/212#issuecomment-369042444  

I've tried with   
  
```  
rule thread-safety-compile ( sources : reqs * : name )  
{  
    return  
    [ compile $(sources)  
        : $(reqs) <toolset>clang:<build>yes <toolset>clang:<cxxflags>-Werror=thread-safety  
        : $(name) ]  
    ;  
}  
```  
  
but it is built even when `<toolset>` is `gcc`.  
  
I believe we need some help from the Boost Build community. Maybe Steven Watanabe could help here.

---

## Comment 4

> Username: thughes  
> Created_at: 2018-02-27 22:11:11 UTC  
> Url: https://github.com/boostorg/thread/pull/212#issuecomment-369044990  

Should I ask on the [boost build](https://lists.boost.org/mailman/listinfo.cgi/boost-build) mailing list?  
  
Looks like Steven is @swatanabe, so maybe he'll see this directly.

---

## Comment 5

> Username: viboes  
> Created_at: 2018-02-27 22:18:12 UTC  
> Url: https://github.com/boostorg/thread/pull/212#issuecomment-369046910  

I've send him a mail.

---

## Comment 6

> Username: viboes  
> Created_at: 2018-02-28 21:14:45 UTC  
> Url: https://github.com/boostorg/thread/pull/212#issuecomment-369385121  

Steven told me that the following should work  
  
```  
rule clang-thread-safety ( properties * )  
{  
    if <toolset>clang in $(properties)  
    {  
        return <cxxflags>-Werror=thread-safety ;  
    }  
    else  
    {  
        return <build>no ;  
    }  
}  
  
rule thread-safety-compile ( sources : reqs * : name )  
{  
    return  
    [ compile $(sources)  
        : $(reqs) <conditional>@clang-thread-safety  
        : $(name) ]  
    ;  
}  
```  
  
I've tested and at least it build and compiles under clang and don't build under gcc.  
  
Please, could you adapt your PR?

---

## Comment 7

> Username: thughes  
> Created_at: 2018-02-28 23:23:27 UTC  
> Url: https://github.com/boostorg/thread/pull/212#issuecomment-369419081  

Looks like it's still failing. I wonder if the issue is that the build specifically sets up a single toolset in `~/user-config.jam`:  
https://github.com/boostorg/thread/blob/f01b7b50133067784da0cab42102ad05a8ae14a9/.travis.yml#L364-L365  
  
http://www.boost.org/build/doc/html/bbv2/overview/configuration.html

---

## Comment 8

> Username: swatanabe  
> Created_at: 2018-02-28 23:37:19 UTC  
> Url: https://github.com/boostorg/thread/pull/212#issuecomment-369421859  

AMDG  
  
On 02/28/2018 04:23 PM, Tom Hughes wrote:  
> Looks like it's still failing. I wonder if the issue is that the build specifically sets up a single toolset in `~/user-config.jam`:  
> https://github.com/boostorg/thread/blob/f01b7b50133067784da0cab42102ad05a8ae14a9/.travis.yml#L364-L365  
>   
> http://www.boost.org/build/doc/html/bbv2/overview/configuration.html  
>   
  
  The problem is that you're explicitly passing <toolset>clang  
in the target requirements, which is unnecessary and wrong.  
(Boost.Build interprets this as: "build this target with  
clang regardless of what the user requested.")  
  
In Christ,  
Steven Watanabe

---

## Comment 9

> Username: thughes  
> Created_at: 2018-02-28 23:52:25 UTC  
> Url: https://github.com/boostorg/thread/pull/212#issuecomment-369424810  

I see, thanks. Can't wait until boost transitions to cmake.

---

## Comment 10

> Username: viboes  
> Created_at: 2018-03-01 08:32:14 UTC  
> Url: https://github.com/boostorg/thread/pull/212#issuecomment-369514570  

The last result are satisfying  
  
```  
clang-linux.compile.c++.without-pth bin.v2/libs/thread/test/mutex__lock_compile_p.test/clang-linux-4.0.1/debug/threading-multi/mutex__lock_compile_p.o  
**passed** bin.v2/libs/thread/test/mutex__lock_compile_p.test/clang-linux-4.0.1/debug/threading-multi/mutex__lock_compile_p.test  
testing.capture-output bin.v2/libs/thread/test/call_once_p_lib.test/clang-linux-4.0.1/debug/threadapi-pthread/threading-multi/call_once_p_lib.run  
**passed** bin.v2/libs/thread/test/call_once_p_lib.test/clang-linux-4.0.1/debug/threadapi-pthread/threading-multi/call_once_p_lib.test  
clang-linux.compile.c++.without-pth bin.v2/libs/thread/test/mutex__lock_compile_f.test/clang-linux-4.0.1/debug/threading-multi/mutex__lock_compile_f.o  
libs/thread/test/sync/mutual_exclusion/mutex/lock_compile_fail.cpp:17:6: error: acquiring mutex 'm0' that is already held [-Werror,-Wthread-safety-analysis]  
  m0.lock();  
     ^  
1 error generated.  
(failed-as-expected) bin.v2/libs/thread/test/mutex__lock_compile_f.test/clang-linux-4.0.1/debug/threading-multi/mutex__lock_compile_f.o  
```  
Thanks Steven for your help.  
  
@thughes I'm wondering if we shouldn't add the <cxxflags>-Werror=thread-safety if available for the positive tests.  
  
Steven has added recently a `check-has-flag` rule, but I have not tried it yet and I don't know how it could be used in this case.  
  
```  
import flags ;  
...  
[ check-has-flag <cxxflags>-Werror=thread-safety  
  : <cxxflags>-Werror=thread-safety  
  : <build>no ]  
```   
  
Would you continue adding annotations?

---

## Comment 11

> Username: thughes  
> Created_at: 2018-03-02 00:06:03 UTC  
> Url: https://github.com/boostorg/thread/pull/212#issuecomment-369775004  

I added annotations for `lock_guard`. I wasn't able to make the initializer list constructor behave correctly, so I left the annotations off of those (which means `make_lock_guard` won't be analyzed correctly either).

---

## Comment 12

> Username: viboes  
> Created_at: 2018-03-02 09:18:46 UTC  
> Url: https://github.com/boostorg/thread/pull/212#issuecomment-369865958  

`make_lock_guard` needs copy elision. I don't know if the current implementation isn't UB.

---

## Comment 13

> Username: thughes  
> Created_at: 2018-03-02 19:16:47 UTC  
> Url: https://github.com/boostorg/thread/pull/212#issuecomment-370023884  

Yeah, I'm not sure if it's that or the initializer list constructor not being supported since I couldn't make the a unit test behave correctly for that on its own. There are some limitations on the analysis:  
https://clang.llvm.org/docs/ThreadSafetyAnalysis.html#known-limitations

---

## Comment 14

> Username: thughes  
> Created_at: 2018-03-02 19:24:54 UTC  
> Updated_at: 2018-03-02 19:33:13 UTC  
> Url: https://github.com/boostorg/thread/pull/212#issuecomment-370026089  

I got impatient waiting for the TravisCI builds, which are slow and tend to hit the [50 minute travis timeout](https://docs.travis-ci.com/user/customizing-the-build#Build-Timeouts), so I made a branch with CircleCI: https://github.com/thughes/thread/blob/feature/add_thread_safety_annotations_circleci/circle.yml  
  
https://circleci.com/gh/thughes/workflows/thread/tree/feature%2Fadd_thread_safety_annotations_circleci  
  
It builds significantly faster (~10-12 mins per variant). It's only doing Linux builds right now because mac builds require contacting support for open source access; I'm also not doing all the same variants as on travis, but that's easy to add. If this is of interest to you I can open a separate PR (and request mac builds). If not, I'll continue to use it separately to keep my sanity.

---

## Comment 15

> Username: viboes  
> Created_at: 2018-03-03 10:59:55 UTC  
> Url: https://github.com/boostorg/thread/pull/212#issuecomment-370138824  

Yes, the test are quite long on Travis.  
  
I didn't know of CircleCI. The times are impressive.  
  
Yes, I'm interested for a separated PR to introduce CircleCi.  
Should I do something specific from my side?

---

## Comment 16

> Username: viboes  
> Created_at: 2018-03-08 21:57:03 UTC  
> Url: https://github.com/boostorg/thread/pull/212#issuecomment-371638276  

I'm working on configuring the timing. I'll let you know when it works, so you could merge it.

---

## Comment 17

> Username: thughes  
> Created_at: 2018-03-08 22:15:25 UTC  
> Updated_at: 2018-03-08 22:16:58 UTC  
> Url: https://github.com/boostorg/thread/pull/212#issuecomment-371643182  

Great, thanks. FWIW, when I tested out the simple change of [subtracting the measured sleep time instead of the hard-coded 250ms](https://github.com/boostorg/thread/pull/213#issuecomment-370934453), it seemed to consistently work on CircleCI and locally (though actually it was slightly negative when doing a `ns` diff, but rounded to 0 ms when using `duration_cast`). (I was just running one of the tests repeatedly in a shell script loop and checking the exit code)  
  
What I saw happening when SSH'ing ([rebuild with SSH](https://discuss.circleci.com/t/circleci-2-0-now-supports-rebuild-with-ssh/11947)) was largely variable `boost::this_thread::sleep_for` times (overwhelming any apparent `try_lock()`, `unlock()`, call etc overhead). I know the measurement is not exactly correct, but it's improvement over the current measurement and probably more reliable than semi-magic constants.

---

## Comment 18

> Username: KindDragon  
> Created_at: 2018-04-25 16:04:08 UTC  
> Url: https://github.com/boostorg/thread/pull/212#issuecomment-384341507  

Do you have any news about this?

---

## Comment 19

> Username: viboes  
> Created_at: 2018-04-25 17:13:24 UTC  
> Url: https://github.com/boostorg/thread/pull/212#issuecomment-384363932  

I've merged. We will see.

---

## Comment 20

> Username: Kojoley  
> Created_at: 2018-04-28 11:42:07 UTC  
> Url: https://github.com/boostorg/thread/pull/212#issuecomment-385168811  

```  
gcc.compile.c++ ../../../../bin.v2/libs/thread/build/gcc-6/release/cxxstd-03-iso/threadapi-pthread/threading-multi/pthread/once.o  
In file included from ../../../../boost/thread/detail/config.hpp:14:0,  
                 from ../../../../libs/thread/src/pthread/once.cpp:6:  
../../../../boost/thread/detail/thread_safety.hpp:26:38: error: anonymous variadic macros were introduced in C++11 [-Werror=variadic-macros]  
 #define BOOST_THREAD_ACQUIRED_BEFORE(...) \  
                                      ^~~  
../../../../boost/thread/detail/thread_safety.hpp:29:37: error: anonymous variadic macros were introduced in C++11 [-Werror=variadic-macros]  
 #define BOOST_THREAD_ACQUIRED_AFTER(...) \  
                                     ^~~  
../../../../boost/thread/detail/thread_safety.hpp:32:31: error: anonymous variadic macros were introduced in C++11 [-Werror=variadic-macros]  
 #define BOOST_THREAD_REQUIRES(...) \  
                               ^~~  
../../../../boost/thread/detail/thread_safety.hpp:35:38: error: anonymous variadic macros were introduced in C++11 [-Werror=variadic-macros]  
 #define BOOST_THREAD_REQUIRES_SHARED(...) \  
                                      ^~~  
../../../../boost/thread/detail/thread_safety.hpp:38:30: error: anonymous variadic macros were introduced in C++11 [-Werror=variadic-macros]  
 #define BOOST_THREAD_ACQUIRE(...) \  
                              ^~~  
../../../../boost/thread/detail/thread_safety.hpp:41:37: error: anonymous variadic macros were introduced in C++11 [-Werror=variadic-macros]  
 #define BOOST_THREAD_ACQUIRE_SHARED(...) \  
                                     ^~~  
../../../../boost/thread/detail/thread_safety.hpp:44:30: error: anonymous variadic macros were introduced in C++11 [-Werror=variadic-macros]  
 #define BOOST_THREAD_RELEASE(...) \  
                              ^~~  
../../../../boost/thread/detail/thread_safety.hpp:47:37: error: anonymous variadic macros were introduced in C++11 [-Werror=variadic-macros]  
 #define BOOST_THREAD_RELEASE_SHARED(...) \  
                                     ^~~  
../../../../boost/thread/detail/thread_safety.hpp:50:34: error: anonymous variadic macros were introduced in C++11 [-Werror=variadic-macros]  
 #define BOOST_THREAD_TRY_ACQUIRE(...) \  
                                  ^~~  
../../../../boost/thread/detail/thread_safety.hpp:53:41: error: anonymous variadic macros were introduced in C++11 [-Werror=variadic-macros]  
 #define BOOST_THREAD_TRY_ACQUIRE_SHARED(...) \  
                                         ^~~  
../../../../boost/thread/detail/thread_safety.hpp:56:31: error: anonymous variadic macros were introduced in C++11 [-Werror=variadic-macros]  
 #define BOOST_THREAD_EXCLUDES(...) \  
                               ^~~  
cc1plus: error: unrecognized command line option ‘-Wno-bool-operation’ [-Werror]  
cc1plus: all warnings being treated as errors  
    "ccache" "g++-6"   -std=c++03 -fPIC -m64 -pthread -O3 -finline-functions -Wno-inline -Wall -pedantic -Werror -Wno-uninitialized -Wno-bool-operation -Wno-sign-compare -Wextra -Wno-long-long -Wno-unused-parameter -Wunused-function -pedantic -DBOOST_ALL_NO_LIB=1 -DBOOST_SYSTEM_DYN_LINK=1 -DBOOST_THREAD_BUILD_DLL=1 -DBOOST_THREAD_DONT_USE_CHRONO -DBOOST_THREAD_POSIX -DNDEBUG  -I"../../../.." -c -o "../../../../bin.v2/libs/thread/build/gcc-6/release/cxxstd-03-iso/threadapi-pthread/threading-multi/pthread/once.o" "../../../../libs/thread/src/pthread/once.cpp"  
```

---

## Comment 21

> Username: viboes  
> Created_at: 2018-09-08 04:10:15 UTC  
> Url: https://github.com/boostorg/thread/pull/212#issuecomment-419611159  

I don't know how to silent those warnings in C++98.   
Should I add pragma push/pop to disable diagnostics?

---

## Comment 22

> Username: viboes  
> Created_at: 2018-09-08 04:11:07 UTC  
> Updated_at: 2018-09-09 11:34:08 UTC  
> Url: https://github.com/boostorg/thread/pull/212#issuecomment-419611203  

@thughes Hi, I had no time to check the consequences of this PR until now :(  
  
There are 3 failling tests  
  
https://www.boost.org/development/tests/develop/developer/thread.html  
  
https://www.boost.org/development/tests/develop/developer/output/Flast-FreeBSD11-boost-bin-v2-libs-thread-test-lock_guard__adopt_lock_compile_p-test-clang-linux-6-0~gnu++11-debug-threading-multi.html  
  
https://www.boost.org/development/tests/develop/developer/output/Flast-FreeBSD11-boost-bin-v2-libs-thread-test-lock_guard__lock_compile_p-test-clang-linux-6-0~gnu++11-debug-threading-multi.html  
  
https://www.boost.org/development/tests/develop/developer/output/Flast-FreeBSD11-boost-bin-v2-libs-thread-test-mutex__lock_compile_p-test-clang-linux-6-0~gnu++11-debug-threading-multi.html  
  
I don't know how to read this errors. If you know, please, could you help me?  
  
I wonder if the following code is not annotated and it shoud  
  
```  
        class pthread_mutex_scoped_lock  
        {  
            pthread_mutex_t* m;  
            bool locked;  
        public:  
            explicit pthread_mutex_scoped_lock(pthread_mutex_t* m_) BOOST_NOEXCEPT:  
                m(m_),locked(true)  
            {  
                BOOST_VERIFY(!pthread_mutex_lock(m));  
            }  
            void unlock() BOOST_NOEXCEPT  
            {  
                BOOST_VERIFY(!pthread_mutex_unlock(m));  
                locked=false;  
            }  
            void unlock_if_locked() BOOST_NOEXCEPT  
            {  
              if(locked)  
              {  
                  unlock();  
              }  
            }  
            ~pthread_mutex_scoped_lock() BOOST_NOEXCEPT  
            {  
                if(locked)  
                {  
                    unlock();  
                }  
            }  
  
        };  
```  
  
There is another error if BOOST_THREAD_HAS_EINTR_BUG is defined as we are iterating until ret != EINTR.  
  
Note that  
  
```  
#ifndef BOOST_THREAD_HAS_NO_EINTR_BUG  
#define BOOST_THREAD_HAS_EINTR_BUG  
#endif  
```  
Wondering if BOOST_THREAD_HAS_NO_EINTR_BUG shouldn't be defined in those cases  
  
  
  
Should these functions be annotated or  the TSA be disabled here?  
  
```  
#ifdef BOOST_THREAD_HAS_EINTR_BUG  
    BOOST_FORCEINLINE int pthread_mutex_destroy(pthread_mutex_t* m)  
    {  
      int ret;  
      do  
      {  
          ret = ::pthread_mutex_destroy(m);  
      } while (ret == EINTR);  
      return ret;  
    }  
    BOOST_FORCEINLINE int pthread_mutex_lock(pthread_mutex_t* m)  
    {  
      int ret;  
      do  
      {  
          ret = ::pthread_mutex_lock(m);  
      } while (ret == EINTR);  
      return ret;  
    }  
    BOOST_FORCEINLINE int pthread_mutex_unlock(pthread_mutex_t* m)  
    {  
      int ret;  
      do  
      {  
          ret = ::pthread_mutex_unlock(m);  
      } while (ret == EINTR);  
      return ret;  
    }  
#else  
    BOOST_FORCEINLINE int pthread_mutex_destroy(pthread_mutex_t* m)  
    {  
      return ::pthread_mutex_destroy(m);  
    }  
    BOOST_FORCEINLINE int pthread_mutex_lock(pthread_mutex_t* m)  
    {  
      return ::pthread_mutex_lock(m);  
    }  
    BOOST_FORCEINLINE int pthread_mutex_unlock(pthread_mutex_t* m)  
    {  
      return ::pthread_mutex_unlock(m);  
    }  
  
#endif  
  
```

---

## Comment 23

> Username: thughes  
> Created_at: 2018-09-08 22:17:35 UTC  
> Url: https://github.com/boostorg/thread/pull/212#issuecomment-419676421  

I think the reason the tests are failing is because FreeBSD has added thread safety annotations on all the `pthread_*` locking methods: https://svnweb.freebsd.org/base?view=revision&revision=270943  
  
It looks like the same thing came up in `libcxx` and they "solved" it by disabling thread annotations on FreeBSD for all the internal libcxx methods that wrap pthread calls:  
https://reviews.llvm.org/rL293197  
https://reviews.llvm.org/D28520?id=83825  
  
It looks like a few of the pthread methods are abstracted in [`boost::posix`](https://github.com/boostorg/thread/blob/33ad8b1834490c270ca5921df5b962f5299b918e/include/boost/thread/pthread/mutex.hpp#L40-L85) which would be a central place to disable, but there are other direct uses of `pthread_` methods in [`include/boost/thread/pthread`](https://github.com/boostorg/thread/tree/develop/include/boost/thread/pthread), so I think those would have to be refactored to use `boost::posix::pthread_` first.  
  
Another alternative would be to just completely disable the thread safety tests when running on FreeBSD.

---

## Comment 24

> Username: thughes  
> Created_at: 2018-09-08 22:50:35 UTC  
> Url: https://github.com/boostorg/thread/pull/212#issuecomment-419677967  

For the variadic macros, is it causing a problem besides the noise?  
  
Looking at the build output with `std=c++98`, it looks like there are a bunch of similar warnings coming from boost::preprocessor (included by boost::test):  
https://circleci.com/gh/boostorg/thread/309  
```  
In file included from ./boost/preprocessor/tuple/elem.hpp:21:0,  
                 from ./boost/preprocessor/seq/for_each.hpp:23,  
                 from ./boost/test/tools/old/interface.hpp:19,  
                 from ./boost/test/test_tools.hpp:45,  
                 from ./boost/test/unit_test.hpp:18,  
                 from libs/thread/test/test_generic_locks.cpp:10:  
./boost/preprocessor/facilities/overload.hpp:22:39: warning: anonymous variadic macros were introduced in C++11 [-Wvariadic-macros]  
 #    define BOOST_PP_OVERLOAD(prefix, ...) BOOST_PP_CAT(prefix, BOOST_PP_VARIADIC_SIZE(__VA_ARGS__))  
```

---

## Comment 25

> Username: viboes  
> Created_at: 2018-09-09 11:31:12 UTC  
> Url: https://github.com/boostorg/thread/pull/212#issuecomment-419709496  

I'm no problem with these warnings personnaly, but maybe people would.  
  
The fact that Boost.Test has them also, is not a justification, as there are a users of Boost.Thread that don't use Boost.Test.

---

## Comment 26

> Username: viboes  
> Created_at: 2018-09-09 11:33:15 UTC  
> Url: https://github.com/boostorg/thread/pull/212#issuecomment-419709586  

@thughes For the FreeBSD restriction, would you mind to provide a PR that do the disable and the refactoring?

---

## Comment 27

> Username: viboes  
> Created_at: 2018-09-09 20:15:11 UTC  
> Updated_at: 2018-09-09 20:17:04 UTC  
> Url: https://github.com/boostorg/thread/pull/212#issuecomment-419741861  

I've tried to add `-Werror=thread-safety` and I'm getting error on unique_lock :(  
I'm trying to disable for  FreBSD, but as I don't have FreBSD and I'm getting errors on the Mac, I'm not sure of  the change.   
  
@thughes Are you able to run the Boost.Thread test on your configuration with -Werror=thread-safety?

---

## Comment 28

> Username: thughes  
> Created_at: 2018-09-10 01:07:50 UTC  
> Url: https://github.com/boostorg/thread/pull/212#issuecomment-419759908  

I had not tried compiling all of the tests with `-Werror=thread-safety`, but I just did and I see what you are describing. The issue is that `boost::unique_lock` is not annotated with thread safety annotations, but it is using the annotated `boost::mutex` class, so analysis is being done on that.  
  
Given that `unique_lock` can do conditional locking, I'm not sure that it can [correctly be annotated](http://lists.llvm.org/pipermail/cfe-dev/2016-November/051465.html) with the current restrictions, such as [lack of move support]((http://clang-developers.42468.n3.nabble.com/Thread-safety-annotations-for-movable-lock-guards-td4039337.html)) and [no conditionally held locks](https://clang.llvm.org/docs/ThreadSafetyAnalysis.html#no-conditionally-held-locks).  
  
It is possible to disable the analysis on `unique_lock`:  
```patch  
--- a/include/boost/thread/lock_types.hpp  
+++ b/include/boost/thread/lock_types.hpp  
@@ -331,7 +331,7 @@ namespace boost  
         m->unlock();  
       }  
     }  
-    void lock()  
+    void lock() BOOST_THREAD_NO_THREAD_SAFETY_ANALYSIS  
     {  
       if (m == 0)  
       {  
@@ -346,7 +346,7 @@ namespace boost  
       m->lock();  
       is_locked = true;  
     }  
-    bool try_lock()  
+    bool try_lock() BOOST_THREAD_NO_THREAD_SAFETY_ANALYSIS  
     {  
       if (m == 0)  
       {  
@@ -436,7 +436,7 @@ namespace boost  
     }  
 #endif  
  
-    void unlock()  
+    void unlock() BOOST_THREAD_NO_THREAD_SAFETY_ANALYSIS  
     {  
       if (m == 0)  
       {  
```  
  
though you still get warnings when you try to do something like this that combines `mutex` and `unique_lock`:  
https://github.com/boostorg/thread/blob/33ad8b1834490c270ca5921df5b962f5299b918e/test/sync/mutual_exclusion/locks/unique_lock/cons/adopt_lock_pass.cpp#L28-L32  
  
If you limit your use to `boost::mutex` and `boost::lock_guard` (as the existing tests that had thread-safety enabled do), then everything's ok. Maybe the best thing to do is to have a global define that enables/disables it so people can opt-in to using it (with the caveats).

---

## Comment 29

> Username: viboes  
> Created_at: 2018-09-10 05:18:40 UTC  
> Url: https://github.com/boostorg/thread/pull/212#issuecomment-419789887  

Please, could you tell me how did you enabled the thread safety on some of the tests and not others?  
  
Yes, I believe that this has been partially done and I shouldn't had accepted this PR.  
Please, could you add a PR that make the feature an opt-in and continue working on it until you have a complete solution?  
  
BTW, as FreeBSD has annotation, does it mean that any software using it and compiled with -Werror=thread-safety will fail?

---

## Comment 30

> Username: viboes  
> Created_at: 2018-09-10 05:40:54 UTC  
> Updated_at: 2018-09-10 05:41:05 UTC  
> Url: https://github.com/boostorg/thread/pull/212#issuecomment-419793140  

Let me know if I understood. Are you saying that the clang Thread Safety Analysis don't work when the function lock/unlock conditionally?  
  
Why do we need to annotate `boost::unique_lock` if we have already added annotations for `boost::mutex`?

---

## Comment 31

> Username: viboes  
> Created_at: 2018-09-10 05:50:15 UTC  
> Url: https://github.com/boostorg/thread/pull/212#issuecomment-419794816  

Oh, I see now how you enabled thread-safety analysis on the Jamfile (with clang-thread-safety).  
  
The problem is that the user can request it for the whole application, as it was the case for the boost regression tester.  
  
Maybe I'm wrong but from what you said and what I'm experimenting, this feature is broken and need to be deactivated.

---

## Comment 32

> Username: thughes  
> Created_at: 2018-09-10 17:05:17 UTC  
> Url: https://github.com/boostorg/thread/pull/212#issuecomment-419987052  

> Please, could you tell me how did you enabled the thread safety on some of the tests and not others?  
  
This is the section that conditionally enables the thread safety compile flag: https://github.com/thughes/thread/blob/1874018c124c33b320e654caaced24d61ec0a757/test/Jamfile.v2#L211-L239  
  
> BTW, as FreeBSD has annotation, does it mean that any software using it and compiled with -Werror=thread-safety will fail?  
  
Yes, even without this change, I _think_ FreeBSD will fail to compile if you use `boost::thread` and enable `Werror=thread-safety` since FreeBSD has annotated all the pthread functions, but the higher-level constructs are not annotated. For example, the errors that came up in this test: https://www.boost.org/development/tests/develop/developer/output/Flast-FreeBSD11-boost-bin-v2-libs-thread-test-lock_guard__adopt_lock_compile_p-test-clang-linux-6-0~gnu++11-debug-threading-multi.html. If it's possible to run the FreeBSD tests on a specific branch, then I could revert these changes and we could try adding `Werror=thread-safety` to verify.   
  
> Are you saying that the clang Thread Safety Analysis don't work when the function lock/unlock conditionally?  
  
Yes, that [appears to be a limitation](Are you saying that the clang Thread Safety Analysis don't work when the function lock/unlock conditionally?).  
  
> Why do we need to annotate `boost::unique_lock` if we have already added annotations for `boost::mutex`?  
  
It appears to be a limitation of [how the analysis works](https://clang.llvm.org/docs/ThreadSafetyAnalysis.html#getting-started). Specifically:  
>  Thread safety analysis is not inter-procedural, so caller requirements must be explicitly declared  
> A function is allowed to acquire a lock without releasing it, (or vice versa), but it must be annotated as such (using `ACQUIRE/RELEASE`)  
  
So it looks like in the case of `unique_lock::lock`, it sees a call to a method that `ACQUIRES` the lock (since `boost::mutex` is annotated with `ACQUIRE`), but doesn't see a corresponding `RELEASE` before the function returns. (This is why I think FreeBSD will fail to compile with `Werror=thread-safety` even without these changes.)  
  
> The problem is that the user can request it for the whole application, as it was the case for the boost regression tester.  
> Maybe I'm wrong but from what you said and what I'm experimenting, this feature is broken and need to be deactivated.  
  
Yes, you are correct. I think it could work for a user's entire application if they limit their use to `boost::mutex` and `boost::lock_guard`, but regardless it should be something that users can opt-in to using (disabled by default) and maybe somehow be marked as experimental. Would the best way to do that be to have a `BOOST_ENABLE_THREAD_ANNOTATIONS` define and wrap the annotations with that?  
before:  
```  
 void lock() BOOST_THREAD_ACQUIRE()  
        {  
```  
after:  
```  
 void lock()   
#ifdef BOOST_ENABLE_THREAD_ANNOTATIONS  
        BOOST_THREAD_ACQUIRE()  
#endif  
        {  
```  
That could also fix the C++98 variadic macros issue since we could disallow `BOOST_ENABLE_THREAD_ANNOTATIONS` when compiling with C++98.

---

## Comment 33

> Username: viboes  
> Created_at: 2018-09-10 19:31:25 UTC  
> Url: https://github.com/boostorg/thread/pull/212#issuecomment-420032243  

Concerning the variadic macros. How many variadic uses do we need, do you have some examples in mind? I'm asking because we could replace variadics by BOOST_THREAD_ACQUIRE_0, BOOST_THREAD_ACQUIRE_1(mu) .... and provide th evariadic BOOST_THREAD_ACQUIRE only when > C++11. Boost.Thread should use the portable interface of course.  
  
I don't know why I don't like too much   
  
```  
 void lock()   
#ifdef BOOST_ENABLE_THREAD_ANNOTATIONS  
        BOOST_THREAD_ACQUIRE()  
#endif  
```  
  
Concerning conditional lock/unlock, I don't see how the this tool can be useful. This is showstopper, isn't it? How can you do in this case to inhibit the false positives?  
  
Concerning the local reasoning, this would mean that the annotations are viral, isn't it? The user needs to annotate all the functions that call an annotated function (with an annotation other than no_thread_safety_analysis annotation). I hope that we can stop when we use the no_thread_safety_analysis annotation.

---

## Comment 34

> Username: viboes  
> Created_at: 2018-09-10 21:54:09 UTC  
> Url: https://github.com/boostorg/thread/pull/212#issuecomment-420073999  

See https://github.com/boostorg/thread/commit/5b209c2e834a89c93d308aa7ddb1dfd1207d929d

---
