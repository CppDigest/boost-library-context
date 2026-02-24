# #17 - Switch to BOOST_NO_CXX11_HDR_ATOMIC ? [Closed]

> Username: htfy96  
> Created at: 2016-02-03 09:11:52 UTC  
> Updated at: 2016-02-03 11:22:43 UTC  
> Closed at: 2016-02-03 09:15:43 UTC  
> Url: https://github.com/boostorg/lockfree/issues/17  

The existence of `<atomic>` not only depends on the version of compiler, but also the version of standard library.   
  
When `detail/atomic.hpp` was created in this project, there was no `BOOST_NO_CXX11_HDR_ATOMIC` (which was introduced around Sep, 2014?) in `boost::config`.  Now `BOOST_NO_CXX11_HDR_ATOMIC` has been well tested and covers far more compilers than `BOOST_LOCKFREE_NO_HDR_ATOMIC`. So I think it's time to switch to `BOOST_NO_CXX11_HDR_ATOMIC` .

---

## Comment 1

> Username: timblechmann  
> Created at: 2016-02-03 09:15:43 UTC  
> Url: https://github.com/boostorg/lockfree/issues/17#issuecomment-179112286  

`BOOST_NO_CXX11_HDR_ATOMIC` doesn't help: some clang versions provided `<atomic>`, but the implementation was broken, so it couldn't be used. `BOOST_NO_CXX11_HDR_ATOMIC` only tells that the header is there, but not that the implementation is valid

---

## Comment 2

> Username: htfy96  
> Created at: 2016-02-03 10:12:07 UTC  
> Url: https://github.com/boostorg/lockfree/issues/17#issuecomment-179144032  

This one is stricter than currrent version:  
  
``` cpp  
#include <boost/config.hpp>  
#if defined(BOOST_NO_CXX11_HDR_ATOMIC) && !defined(BOOST_LOCKFREE_NO_HDR_ATOMIC)  
#  define BOOST_LOCKFREE_NO_HDR_ATOMIC  
#endif  
```

---

## Comment 3

> Username: timblechmann  
> Created at: 2016-02-03 11:22:43 UTC  
> Url: https://github.com/boostorg/lockfree/issues/17#issuecomment-179175776  

could you send me a pull request?
