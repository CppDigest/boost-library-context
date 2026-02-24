# #20 Support Xcode [Closed]

> Username: thejustinwalsh  
> Created at: 2015-09-24 07:19:38 UTC  
> Updated at: 2015-09-27 18:24:20 UTC  
> Closed at: 2015-09-27 18:24:20 UTC  
> Url: https://github.com/boostorg/context/pull/20  

boost.context is not compatible with Xcode versions greater than 6.1 (as far as my google skills lead me to believe). A few things are required to work it out. I don't expect this pull request to be excepted as is, but wanted to provide something that builds as an example of what will need to be done.  
#### config.hpp  
  
I've added an additional workaround for detecting integer sequence w/ Xcode versions using clang 3.4 and up. if `_LIBCPP_STD_VER > 11` is pulled from the `<utility>` and is defined in this case.  
#### execution_context.ipp  
  
Apple decided to block support for thread local storage for whatever reason, and it won't compile as is. Unfortunately I know nothing about thread local storage, and found this hack from a pull request that was submitted to Textmate. This change brings in a dependency on boost_thread. Pretty sure this change is just as good as handing off static memory, since you would need to pass around the local storage wrapper to get it cleaned up properly.  
  
_Compiling, building, running on Xcode 7._

---

## Comment 1

> Username: thejustinwalsh  
> Created_at: 2015-09-25 19:54:26 UTC  
> Url: https://github.com/boostorg/context/pull/20#issuecomment-143336815  

[According to Apple](https://devforums.apple.com/thread/251372?tstart=0) they have disabled the C++11 keyword thread_local. My guess is that I will need to compile boost with clang directly and link it in.  
  
That being said, a workaround is still needed from the header above for clang 3.4 as that is the version that Xcode forks off of for their in house clang fork.

---

## Comment 2

> Username: olk  
> Created_at: 2015-09-27 18:24:20 UTC  
> Url: https://github.com/boostorg/context/pull/20#issuecomment-143582450  

thank you for your patches.  
I'll take the fix related to integer_sequence, but I don't like to replace thread_local by thread_specific_ptr (which is much slower) in order to fix a problem with one compiler (no idea why Apple is unable to implement thread_local).

---
