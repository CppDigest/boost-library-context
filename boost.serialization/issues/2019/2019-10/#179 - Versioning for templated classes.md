# #179 - Versioning for templated classes [Open]

> Username: HDembinski  
> Created at: 2019-10-20 09:30:29 UTC  
> Updated at: 2019-10-20 09:30:29 UTC  
> Url: https://github.com/boostorg/serialization/issues/179  

Boost.Serialization has no buidlin support for versioning a templated class. Modern libraries often have templated classes, so adding support for this is important. [The tutorial does not discuss this case](https://www.boost.org/doc/libs/1_71_0/libs/serialization/doc/tutorial.html#versioning), and [the version.hpp header has no advice either](https://www.boost.org/doc/libs/1_71_0/boost/serialization/version.hpp).  
  
On a compiler which supports partial template specialization, one can just specialize the version struct "by hand", this is what I did for Boost.Histogram. It looks similar to this minimal code  
https://godbolt.org/z/tX4sRX  
  
The only thing missing is a nifty macro for this.
