# #304 - BOOST_THREAD_USES_CHRONO not consistent on build and use [Open]

> Username: pdimov  
> Created at: 2020-01-01 18:05:41 UTC  
> Updated at: 2020-06-25 17:39:05 UTC  
> Url: https://github.com/boostorg/thread/issues/304  

As reported in https://github.com/boostorg/boost_install/issues/27, the following program:  
```  
#include <boost/thread/condition_variable.hpp>  
  
int main() {  
    boost::condition_variable cv;  
    boost::mutex mutex;  
    boost::unique_lock<boost::mutex> lock(mutex);  
    cv.wait_for(lock, boost::chrono::seconds(1));  
    return 0;  
}  
```  
compiles on Linux but doesn't link without specifying `-lboost_chrono`, even though the Boost.Thread build/Jamfile doesn't specify Chrono as a dependency on Pthread platforms. The reason is that the Jamfile defines `BOOST_THREAD_DONT_USE_CHRONO` on Pthread platforms when Boost.Thread is built. However, when Boost.Thread is used by user code, `BOOST_THREAD_DONT_USE_CHRONO` is not defined and  https://github.com/boostorg/thread/blob/develop/include/boost/thread/detail/config.hpp#L143 defines `BOOST_THREAD_USES_CHRONO`. This enables the `wait_for` overload and allows the code above to compile, even though the compiled library wasn't built with `BOOST_THREAD_USES_CHRONO`.  
  
I'm not sure whether this behavior can be considered correct. One may argue that `BOOST_THREAD_USES_CHRONO` should not be defined on Pthread platforms by default, to match the settings with which the library is built.

---

## Comment 1

> Username: pdimov  
> Created at: 2020-01-01 18:17:50 UTC  
> Url: https://github.com/boostorg/thread/issues/304#issuecomment-570072651  

Or, maybe the best way to solve this is to just have Thread always depend on Chrono, even on Pthread platforms.

---

## Comment 2

> Username: viboes  
> Created at: 2020-01-28 17:42:02 UTC  
> Url: https://github.com/boostorg/thread/issues/304#issuecomment-579370000  

I don't remember why it is different for pthreads and windows.   
In any case, if the Jambile of the library defines   
  
  
```  
        result += <define>BOOST_THREAD_DONT_USE_CHRONO ;  
```  
or   
  
```  
        result += <define>BOOST_THREAD_USES_CHRONO ;  
```  
  
I don't see what a user wanting the opposite can do.

---

## Comment 3

> Username: pdimov  
> Created at: 2020-01-28 17:53:46 UTC  
> Url: https://github.com/boostorg/thread/issues/304#issuecomment-579374843  

The Jamfile define is only applied when the library is built; but once it's built, packaged, then installed with `apt install`, the user who simply includes `<boost/thread/condition_variable.hpp>` gets `BOOST_THREAD_USES_CHRONO` automatically defined by `boost/thread/detail/config.hpp`, whereas at build time the Jamfile has defined the opposite.  
  
This happens to work fine only because all uses of `BOOST_THREAD_USES_CHRONO` are inline so there aren't any link errors or ODR violations.  
  
We could leave this as is, but Linux CMake users will have to link to Boost::chrono by hand in addition to Boost::thread.  
  
Or, we could make BOOST_THREAD_USES_CHRONO default on Linux as well, so that Jamfile and config.hpp agree.

---

## Comment 4

> Username: maikelvdh  
> Created at: 2020-06-25 17:39:05 UTC  
> Url: https://github.com/boostorg/thread/issues/304#issuecomment-649723159  

@viboes would it be an option to link Chrono by default in case of pthread? This problem is namely causing some issues when we were are trying to utilise the produced BoostConfig.cmake file.
