# #1 Add support for lambdas to boost::generator_iterator. [Closed]

> Username: LEW21  
> Created at: 2013-12-16 19:37:14 UTC  
> Updated at: 2017-01-27 21:16:32 UTC  
> Closed at: 2017-01-27 21:16:32 UTC  
> Url: https://github.com/boostorg/utility/pull/1  

Now the example is much nicer ;)

---

## Comment 1

> Username: Lastique  
> Created_at: 2017-01-27 21:16:32 UTC  
> Url: https://github.com/boostorg/utility/pull/1#issuecomment-275776658  

1. `generator_iterator.hpp` is part of Boost.Iterator now.  
2. The proposed change makes `generator_iterator` incompatible with C++03 compilers. While I'm not opposed to support for lambdas, it should not result in breaking backward compatibility.  
  
Thus, as the PR needs to be reworked anyway, I'm closing it now.

---
