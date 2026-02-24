# #81 - `mpfr_const_bla` family of constants  🥧 [Closed]

> Username: ofloveandhate  
> Created at: 2018-08-31 17:59:37 UTC  
> Updated at: 2018-08-31 18:09:37 UTC  
> Closed at: 2018-08-31 18:09:37 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/81  

I sometimes need π.  I am not sure I trust the one in Boost.Math to provide π to arbitrary precision.  should i?  to be 100% confident, i petition for the exposure of `mpfr_const_pi` into Boost.Multiprecision.

---

## Comment 1

> Username: jzmaddock  
> Created at: 2018-08-31 18:09:37 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/81#issuecomment-417747291  

Yes Boost.Math constants are arbitrary precision, but in any case for performance reasons there are already specializations of the Math lib constants in mpfr.hpp for PI, euler, ln2 and catalan.  Which is to say that `boost::math::constants::pi<mpfr_float>() will call `mpfr_const_pi` already.
