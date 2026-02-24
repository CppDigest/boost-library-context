# #623 Add standalone BOOST_HAS_THREADS [Merged]

> Username: mborland  
> Created at: 2021-05-04 17:47:08 UTC  
> Updated at: 2021-05-08 18:13:17 UTC  
> Merged at: 2021-05-08 15:06:14 UTC  
> Closed at: 2021-05-08 15:06:14 UTC  
> Url: https://github.com/boostorg/math/pull/623  

As mentioned in issue #621. Removes hard errors for not having thread support headers. Default assumption is that a particular platform has threads available.

---

## Comment 1

> Username: ckormanyos  
> Created_at: 2021-05-04 18:55:18 UTC  
> Url: https://github.com/boostorg/math/pull/623#issuecomment-832168749  

This seems cool. What is planned for code actually using something like `std::mutex`? Will it simply be disabled with compiler switch or will the function be stubed/mocked with a dummy?  
  
I have been reticent and slow to do my part on bare metal. But we're probably looking at something similar for I/O streaming and exceptions.

---

## Comment 2

> Username: mborland  
> Created_at: 2021-05-05 16:10:39 UTC  
> Url: https://github.com/boostorg/math/pull/623#issuecomment-832820342  

> This seems cool. What is planned for code actually using something like `std::mutex`? Will it simply be disabled with compiler switch or will the function be stubed/mocked with a dummy?  
>   
  
I think that disabling via compiler switching is the best route. There were previously branches in some functions (e.g. bernoulli numbers) that I can just put back with this change.  
  
@jzmaddock Pending further review the CI is clean.

---

## Review 3 [Commented]

> Username: jzmaddock  
> Created_at: 2021-05-05 17:35:57 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/623#pullrequestreview-652576755  

📁 include/boost/math/tools/config.hpp

```diff
  50 | #define BOOST_DO_STRINGIZE(X) #X
  51 | 
  52 |+ // Detect thread support via STL implementation
```

> Username: jzmaddock  
> Created_at: 2021-05-05 17:35:57 UTC  
> Url: https://github.com/boostorg/math/pull/623#discussion_r626767519  

Thanks Matt, however I suspect this is going to cause macro-redefinition conflicts unless guarded by a check that we're in standalone mode.  
  
BTW BOOST_DISABLE_THREADS, while set inside Boost.Config is intended as a macro the user can set on the command line to force thread support off (for example if they just don't need it, even though it's supported, see https://www.boost.org/doc/libs/1_76_0/libs/config/doc/html/index.html#boost_config.configuring_boost_for_your_platform.user_settable_options), so there should be a check to see it's set already and configure appropriately.  
  
A small point of pedantry: most std libraries will supply `<mutex>` etc even though they may be empty or non-functional in the current compilation mode.  Which is to say further down the road we may need to pull in some of the logic from Boost.Config here for popular platforms at least.  
  
I might also be inclined to set BOOST_NO_CXX11_HDR_ATOMIC, BOOST_NO_CXX11_HDR_FUTURE, BOOST_NO_CXX11_HDR_MUTEX, BOOST_NO_CXX11_HDR_THREAD and BOOST_NO_CXX11_THREAD_LOCAL when BOOST_DISABLE_THREADS is set so that in code we can support partial conformance if needs be.  So I guess it would look something like:  
  
```  
#ifdef BOOST_MATH_STANDALONE  
#ifdef BOOST_DISABLE_THREADS  
// no threads!  
#elif defined(__has_include)  
#  if !__has_include(<thread>) || !__has_include(<mutex>) || !__has_include(<future>) || !__has_include(<atomic>)  
#     define BOOST_DISABLE_THREADS  
#  else  
#     define BOOST_HAS_THREADS  
#  endif   
#else  
#  define BOOST_HAS_THREADS // The default assumption is that the machine has threads  
#endif // Thread Support  
//  
// Compiler specific config may go here later....  
//  
// When threads are disabled, set the individual Boost.Config thread related macros:  
//  
#ifdef BOOST_DISABLE_THREADS  
#  define BOOST_NO_CXX11_HDR_ATOMIC  
#  define BOOST_NO_CXX11_HDR_FUTURE  
#  define BOOST_NO_CXX11_HDR_MUTEX  
#  define BOOST_NO_CXX11_HDR_THREAD  
#  define BOOST_NO_CXX11_THREAD_LOCAL  
#endif // BOOST_DISABLE_THREADS  
#endif // BOOST_MATH_STANDALONE  
```


---

## Comment 4

> Username: ckormanyos  
> Created_at: 2021-05-05 18:19:19 UTC  
> Updated_at: 2021-05-05 18:20:28 UTC  
> Url: https://github.com/boostorg/math/pull/623#issuecomment-832907048  

The following suggestion might seem ridiculous, it might be reasonable. I'm not really experienced enough to judge...  
  
I was thinking we could mentally or via code search identify all uses of `<atomic>`, `<mutex>` and `<thread>` and see if we could write dummy placeholders for them (isolated in a boost-math-local unique namespace).  
  
We could then configure to use real function/dummy function at one place?

---

## Comment 5

> Username: mborland  
> Created_at: 2021-05-08 14:19:39 UTC  
> Url: https://github.com/boostorg/math/pull/623#issuecomment-835381729  

@jzmaddock This now is clean with your requested changes.

---

## Comment 6

> Username: jzmaddock  
> Created_at: 2021-05-08 15:06:06 UTC  
> Url: https://github.com/boostorg/math/pull/623#issuecomment-835396003  

Thanks Matt!

---
