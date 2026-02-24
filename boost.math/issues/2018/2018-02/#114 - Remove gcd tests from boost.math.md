# #114 - Remove gcd tests from boost.math? [Closed]

> Username: NAThompson  
> Created at: 2018-02-16 16:31:08 UTC  
> Updated at: 2018-02-16 18:45:06 UTC  
> Closed at: 2018-02-16 18:45:06 UTC  
> Url: https://github.com/boostorg/math/issues/114  

It appears that the gcd is tested in boost.integer, but the tests are somewhat different. Does it make sense to remove the gcd unit tests from boost.math?

---

## Comment 1

> Username: jzmaddock  
> Created at: 2018-02-16 17:18:56 UTC  
> Url: https://github.com/boostorg/math/issues/114#issuecomment-366299891  

Even though they're obsolete I retained them as I wanted to check backwards compatibility for anyone still using boost::math::gcd, but yes, now that the headers scream "deprecated" at you, I think we probably can remove them.

---

## Comment 2

> Username: NAThompson  
> Created at: 2018-02-16 18:45:06 UTC  
> Url: https://github.com/boostorg/math/issues/114#issuecomment-366323564  

See #115
