# #86 - boost/random/detail/polynomial.hpp:303:20: -Wdeprecated-copy warning [Open]

> Username: Kojoley  
> Created at: 2021-12-15 00:59:59 UTC  
> Updated at: 2024-08-16 23:15:30 UTC  
> Url: https://github.com/boostorg/random/issues/86  

```  
clang-linux.compile.c++ ../../../../bin.v2/libs/spirit/test/qi/qi_range_run.test/clang-linux-12/release/cxxstd-11-iso/stdlib-libc++/threading-multi/visibility-hidden/range_run.o  
In file included from range_run.cpp:17:  
In file included from ../../../../boost/random/mersenne_twister.hpp:32:  
../../../../boost/random/detail/polynomial.hpp:303:20: error: definition of implicit copy constructor for 'reference' is deprecated because it has a user-declared copy assignment operator [-Werror,-Wdeprecated-copy]  
        reference &operator=(const reference &other)  
                   ^  
../../../../boost/random/detail/polynomial.hpp:315:16: note: in implicit copy constructor for 'boost::random::detail::polynomial::reference' first required here  
        return reference(_storage[i/bits], i%bits);  
               ^  
```

---

## Comment 1

> Username: jcstroud  
> Created at: 2024-08-16 11:15:49 UTC  
> Url: https://github.com/boostorg/random/issues/86#issuecomment-2293316836  

I created a pull request to fix this issue: https://github.com/boostorg/random/pull/110  
  
The fix is a simple, two line addition.

---

## Comment 2

> Username: jcstroud  
> Created at: 2024-08-16 23:15:28 UTC  
> Url: https://github.com/boostorg/random/issues/86#issuecomment-2294433923  

The previous fix did not pass all tests, seemingly because of backwards compatibility. I made a new, backwards compatible pull request here: https://github.com/boostorg/random/pull/111
