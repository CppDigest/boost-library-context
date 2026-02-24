# #60 Adaptive tanh-sinh (double exponential) quadrature [Closed]

> Username: NAThompson  
> Created at: 2017-03-10 21:31:28 UTC  
> Updated at: 2017-05-12 02:25:55 UTC  
> Closed at: 2017-05-11 04:04:35 UTC  
> Url: https://github.com/boostorg/math/pull/60  

This commit rounds out the feature set of the proposed numerical quadrature extension to boost::math, which will hopefully facilitate a more productive discussion about whether it should be merged. Presumably a release would also have an adaptive Simpson, a Romberg, a Gauss-Konrod, and perhaps a sampled trapezoidal and a sampled Simpson. However, these can be added if this is viewed as a good addition to the library.

---

## Comment 1

> Username: pabristow  
> Created_at: 2017-03-11 10:03:31 UTC  
> Url: https://github.com/boostorg/math/pull/60#issuecomment-285856898  

Looks very useful and very good to me. (But I suspect John is very busy with a Boost review of safe numerics at present).

---

## Comment 2

> Username: NAThompson  
> Created_at: 2017-03-11 16:10:32 UTC  
> Url: https://github.com/boostorg/math/pull/60#issuecomment-285877736  

Hey no problem; I figured this might not get in until after 1.64 is finished. The reason I keep pushing more diffs is it gives you guys confidence that there is a minimal feature set that could be useful in a large number of cases. Let me know if there's anything I could do to make the review easier.

---

## Comment 3

> Username: jeremy-murphy  
> Created_at: 2017-03-14 12:30:33 UTC  
> Url: https://github.com/boostorg/math/pull/60#issuecomment-286406670  

Again, thanks for implementing some great numerical analysis algorithms.  
One suggestion I would make, is to actually consider decomposing the design. That is, continue to provide the class interface, but implement the algorithms as free functions that are used by the class member functions. The reason is to increase flexibility (without compromising ease of use).  
The class interface has some design choices built in, such as copying the x and y data, that might not suit everyone. By leaving the 'internals' exposed, someone can easily write a new class interface of their own design choices that still uses these core algorithms.  
Another, slightly more controversial (perhaps) suggestion, is to separate the core algorithm and the error checking. The reason again is modularity: sometimes it might be useful to check the the correctness without running the algorithm, and sometimes you already know the correctness and you really just want to run the algorithm.  
It's all in the argumentative space of 'design', which I think is important, but don't feel obliged to take me too seriously. I've made very general suggestions but I can be more specific if you would like.

---

## Comment 4

> Username: NAThompson  
> Created_at: 2017-03-14 14:10:55 UTC  
> Url: https://github.com/boostorg/math/pull/60#issuecomment-286432694  

> That is, continue to provide the class interface, but implement the algorithms as free functions that are used by the class member functions.   
  
Actually, I originally wrote the tanh-sinh as a free function, but unfortunately there are two details which make it difficult: The first is that the precomputed abscissas and weights must be lexically cast to their appropriate types. I believe that `boost::lexical_cast` can be called at compile time, but in my benchmarks I saw it called at runtime. The second is that the algorithm needs to find the maximum `t` such that `tanh(half_pi<Real>()*sinh(t)` is strictly less than one. This allows the algorithm to get high precision on integrands with endpoint singularities. However, determining this value takes ~200ns to compute, which exceeds the time to compute the integral. Since many people will use this to assemble a stiffness matrix, paying this one time cost via a constructor and then using the cached values seemed reasonable. But nonetheless I'd like to find some way to avoid implementing it as a class, I'm just not sure how given these constraints.  
  
> Another, slightly more controversial (perhaps) suggestion, is to separate the core algorithm and the error checking.  
  
Yeah, I definitely should get on board and start using the `policies`. I'll try to get a commit in that fixes this.  
  
> I've made very general suggestions but I can be more specific if you would like.  
  
Absolutely; I think there's definitely some ideas that could be expressed more clearly in that code, I'd love to get some more feedback.

---

## Comment 5

> Username: NAThompson  
> Created_at: 2017-03-14 15:43:21 UTC  
> Url: https://github.com/boostorg/math/pull/60#issuecomment-286462647  

The correct design, IMO, is to have a wrapper for all the quadrature algorithms, and then have an argument that is passed which indicates your belief about the function, say, `periodic`, `holomorphic`, `continuous`, or `oscillatory`. Then have the wrapper choose the faster routine for each function class. How feasible this actually will be is unclear at this point . . .

---

## Comment 6

> Username: pabristow  
> Created_at: 2017-03-14 16:25:07 UTC  
> Url: https://github.com/boostorg/math/pull/60#issuecomment-286476659  

Might 'belief' be feasible with a template parameter so it can be a compile-time choice?

---

## Comment 7

> Username: NAThompson  
> Created_at: 2017-03-20 16:40:35 UTC  
> Url: https://github.com/boostorg/math/pull/60#issuecomment-287819458  

The macros seems more maintainable, but I had to put in a compiler flag `--U__STRICT_ANSI__` in order to get it to work with `g++`. My compiler flags are  
  
     g++ -U__STRICT_ANSI__ --std=c++14 -g -O0 -march=native -Wfatal-errors -fext-numeric-literals foo.cpp -lquad  
  
Maybe the macro needs some TLC? Here's it's definition:  
  
    #if defined(_GLIBCXX_USE_FLOAT128) && !defined(__STRICT_ANSI__) && !defined(BOOST_NVCC_CXX03)  
    # define BOOST_HAS_FLOAT128  
    #endif

---

## Comment 8

> Username: pabristow  
> Created_at: 2017-03-21 08:51:07 UTC  
> Url: https://github.com/boostorg/math/pull/60#issuecomment-288013169  

Is the logic of this that strict ANSI does/should not allow non-standard quad?  But John Maddock is your expert on this.  The macro allows portableness by switching to the multiprecision type boost::multiprecision::cpp_bin_float_quad if builtin quad (or Intel version) is not available.  
  
John has written a cunning macro that allows one to provide test values (or of the appropriate precision from 9 for float up to 50 decimal digits for cpp_dec_float_50 to all the builtin and multiprecision types.  This might become mainstream.  But for your testing purposes boost::multiprecision::cpp_bin_float_quad might be more than enough?  Portable if much slower than quad but probably that doesn't matter?

---

## Comment 9

> Username: jzmaddock  
> Created_at: 2017-03-21 09:42:31 UTC  
> Url: https://github.com/boostorg/math/pull/60#issuecomment-288025413  

On 21/03/2017 08:51, Paul A. Bristow wrote:  
>  
> Is the logic of this that strict ANSI does/should not allow   
> non-standard quad? But John Maddock is your expert on this. The macro   
> allows portableness by switching to the multiprecision type   
> boost::multiprecision::cpp_bin_float_quad if builtin quad (or Intel   
> version) is not available.  
>  
  
 From memory there are some compiler configurations which you can't tell   
apart - one doesn't accept __float128 and one does :(  
  
I'm backed up at the moment, but I will look at this again, John.  
  
---  
This email has been checked for viruses by AVG.  
http://www.avg.com

---

## Comment 10

> Username: NAThompson  
> Created_at: 2017-03-21 13:56:51 UTC  
> Url: https://github.com/boostorg/math/pull/60#issuecomment-288085636  

>  But for your testing purposes boost::multiprecision::cpp_bin_float_quad might be more than enough? Portable if much slower than quad but probably that doesn't matter?  
  
The reason I want to scale through multiple precisions in the unit test is mainly to ensure that roundoff error doesn't cause accidental evaluation of the function at an endpoint. So the performance is a non-issue. I will switch to `cpp_bin_float_quad`.

---

## Comment 11

> Username: pabristow  
> Created_at: 2017-03-21 14:22:48 UTC  
> Url: https://github.com/boostorg/math/pull/60#issuecomment-288093599  

You might find this work-in-progress useful?  Actually a .hpp of course. Comments welcome on whether this should become 'mainstream'.  
[test_value.txt](https://github.com/boostorg/math/files/858392/test_value.txt)

---

## Comment 12

> Username: NAThompson  
> Created_at: 2017-03-21 14:54:50 UTC  
> Url: https://github.com/boostorg/math/pull/60#issuecomment-288104263  

> // (Constructing from double or long double (or quad) could lose precision for multiprecision or fixed-point).  
  
In `tanh_sinh_detail.hpp`, I was simply using `boost::lexical_cast<Real>("3.14157...")` for the abscissas and weights. So I'm wondering if the existence of this file indicates that this is not an ideal approach?

---

## Comment 13

> Username: pabristow  
> Created_at: 2017-03-21 15:14:38 UTC  
> Url: https://github.com/boostorg/math/pull/60#issuecomment-288110885  

Well that *should* work, but it relies on lexical_cast and for fancy types it might not (though that's the fallback from John's complicated code when it is not possible to construct/convert directly from long double (builtins) or construct from a decimal digit string (multiprecision and prototype fixed-point types)).    
  
lexical_cast will aways be slower than double d = 1.23456789012345678L  
  
However John worked this up at my request for an easy way to extend tests to the fancy types.  It is still slower than a plain assignment and so not so practial if you have lots and lots of values to test.  So still work in progress and not necessarily for you, perhaps just a distraction?

---

## Comment 14

> Username: jzmaddock  
> Created_at: 2017-03-21 17:32:37 UTC  
> Url: https://github.com/boostorg/math/pull/60#issuecomment-288157086  

On 21/03/2017 14:54, Nick wrote:  
>  
>     // (Constructing from double or long double (or quad) could lose  
>     precision for multiprecision or fixed-point).  
>  
> In |tanh_sinh_detail.hpp|, I was simply using   
> |boost::lexical_cast<Real>("3.14157...")| for the abscissas and   
> weights. So I'm wondering if the existence of this file indicates that   
> this is not an ideal approach?  
>  
  
For headers,  
  
boost::math::constants::pi<T>()  
  
would be much preferred.  
  
Best, John.  
  
---  
This email has been checked for viruses by AVG.  
http://www.avg.com

---

## Comment 15

> Username: NAThompson  
> Created_at: 2017-05-11 04:04:35 UTC  
> Url: https://github.com/boostorg/math/pull/60#issuecomment-300676605  

Merge conflicts will be painful to deal with; I'm reopening elsewhere.

---
