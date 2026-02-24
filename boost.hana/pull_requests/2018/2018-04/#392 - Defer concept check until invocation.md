# #392 [lift] Defer concept check until invocation [Merged]

> Username: ricejasonf  
> Created at: 2018-04-06 21:58:18 UTC  
> Updated at: 2018-04-08 05:34:01 UTC  
> Merged at: 2018-04-08 05:33:49 UTC  
> Closed at: 2018-04-08 05:33:49 UTC  
> Url: https://github.com/boostorg/hana/pull/392  

Here is my use case to justify this:  
  
```cpp  
#include <boost/hana/fwd/lift.hpp>  
  
namespace full_duplex {  
    struct promise_tag { };  
  
    struct promise_fn {  
        template <typename AsyncFn>  
        constexpr auto operator()(AsyncFn&&) const;  
    };  
  
    constexpr promise_fn promise{};  
  
    constexpr boost::hana::lift_t<promise_tag> promise_lift;  
    // ^ requires promise_tag to be an Applicative before there is a chance to implement  
}  
```  
BTW  
I'm making a clean break and implementing my promise stuff as a proper `hana::Monad`.  
Shameless plug: [Generalized Full Duplex Messaging](http://sched.co/EC73)

---

## Comment 1

> Username: ldionne  
> Created_at: 2018-04-07 16:33:48 UTC  
> Url: https://github.com/boostorg/hana/pull/392#issuecomment-379482192  

Your change makes Doxygen [emit a warning](https://travis-ci.org/boostorg/hana/jobs/363325746#L1648); please use `//! @cond` and `// @endcond` around the definition of `operator()` -- I think that should make it go away.

---

## Comment 2

> Username: ricejasonf  
> Created_at: 2018-04-07 21:14:13 UTC  
> Url: https://github.com/boostorg/hana/pull/392#issuecomment-379499673  

Fixed the warning  
```  
root@873199c26702:/opt/build# make doc  
Scanning dependencies of target doc  
Generating API documentation with Doxygen  
Built target doc  
root@873199c26702:/opt/build#  
```

---

## Comment 3

> Username: ldionne  
> Created_at: 2018-04-08 05:34:01 UTC  
> Url: https://github.com/boostorg/hana/pull/392#issuecomment-379521897  

Thanks for the PR!

---
