# #47 - uniform_01 generator [Open]

> Username: DiscreteLogarithm  
> Created at: 2018-10-23 10:26:54 UTC  
> Updated at: 2018-10-27 07:31:55 UTC  
> Url: https://github.com/boostorg/random/issues/47  

There's this comment in `uniform_01.hpp`:  
  
> Note: The current implementation is buggy, because it may not fill  
> all of the mantissa with random bits. I'm unsure how to fill a  
> (to-be-invented) @c boost::bigfloat class with random bits efficiently.  
  
We do have an efficient algorithm ([see section 4 of this preprint](https://arxiv.org/pdf/1810.04744.pdf)) and a [C++ implementation](https://github.com/DiscreteLogarithm/canonical-random-float) capable of generating all representable floating-point numbers in [0,1). It fills the mantissa with a random integer and generates a geometric random number for the exponent. In the double precision case, it is even faster than simply multiplying a 64-bit integer by 2<sup>-64</sup> and in the single precision case it's only 25% slower than multiplying a 32-bit integer by 2<sup>-32</sup> (when used with the MT19937 URBG).  
  
If you are interested we can help to integrate it into boost.
