# #44 - uniform_real_distribution should use uniform_int_distribution [Open]

> Username: drauch  
> Created at: 2018-09-22 17:27:08 UTC  
> Updated at: 2018-10-28 20:31:27 UTC  
> Url: https://github.com/boostorg/random/issues/44  

Hello again!  
  
`generate_uniform_real` for `is_integral<Engine::result_type> == true` should probably do something like this (pseudo code):  
  
    auto dist = uniform_int_distribution<unsigned long>( unsigned long min, unsigned long max );  
    auto numerator = dist(eng);  
    auto divisor = unsigned long max;  
    [...]  
  
instead of using:  
  
    result_type numerator = static_cast<T>(subtract<base_result>()(eng(), (eng.min)()));  
    result_type divisor = static_cast<T>(subtract<base_result>()((eng.max)(), (eng.min)())) + 1;  
    [...]  
  
The current implementation fails to produce more different values than the range of the Engine is able to give. E.g., a PRNG which only ever produces values in the range [0..1] would lead to returning only 2 different real values.  
  
What are your thoughts about this?  
  
Best regards,  
Dominik  
  
PS: I noticed this when using good ol' `rand()` as an Engine behind the scenes.

---

## Comment 1

> Username: swatanabe  
> Created at: 2018-09-22 17:55:22 UTC  
> Url: https://github.com/boostorg/random/issues/44#issuecomment-423761825  

AMDG  
  
On 09/22/2018 11:27 AM, drauch wrote:  
> `generate_uniform_real` for `is_integral<Engine::result_type>` should probably do something like this (pseudo code):  
>   
>     auto dist = uniform_int_distribution<unsigned long>( unsigned long min, unsigned long max );  
>     auto numerator = dist(eng);  
>     auto divisor = unsigned long max;  
>     [...]  
>   
> instead of using:  
>   
>     result_type numerator = static_cast<T>(subtract<base_result>()(eng(), (eng.min)()));  
>     result_type divisor = static_cast<T>(subtract<base_result>()((eng.max)(), (eng.min)())) + 1;  
>     [...]  
>   
> The current implementation fails to produce more different values than the range of the Engine is able to give. E.g., a PRNG which only ever produces values in the range [0..1] would lead to returning only 2 different real values.  
>   
> What are your thoughts about this?  
>   
  
- Using uniform_int_distribution is not really the correct  
  choice.  You can't just use unsigned long because that  
  may lose precision.  Even if you try to pick some sufficiently  
  large integral type, it may not work for multiprecision floats.  
- There is a workaround for the problem in the form of  
  independent_bits_engine.  
- generate_canonical allows you to specify the number of  
  bits that you want.  
- This is a long-standing known issue.  uniform_01 has a  
  comment about it.  
  
In Christ,  
Steven Watanabe

---

## Comment 2

> Username: drauch  
> Created at: 2018-09-22 19:20:38 UTC  
> Updated at: 2018-09-22 19:21:19 UTC  
> Url: https://github.com/boostorg/random/issues/44#issuecomment-423767118  

As an entry-level C++ programmer I haven't heard about Boost's multiprecision floats until now - nice thingy :-). I thought that unsigned long is covering enough grounds for double (which it is, imho), but you're right, one had to choose an integral type which has the same amount of bits as the corresponding real type has - is there some kind of `big_integral<bits>` type in Boost as well which we could use?  
  
Thanks for your pointers, as I'm trying to translate some of boost::random to C#, and the highest supported type of my library is going to be C#'s double, I conclude that I can go with uniform_int_distribution<unsigned long>.  
  
Thanks again, you're really helpful with your quick answers!

---

## Comment 3

> Username: swatanabe  
> Created at: 2018-09-22 19:41:16 UTC  
> Url: https://github.com/boostorg/random/issues/44#issuecomment-423768284  

AMDG  
  
On 09/22/2018 01:20 PM, drauch wrote:  
> As an entry-level C++ programmer I haven't heard about Boost's multiprecision floats until now - nice idea, I thought that unsigned long is covering enough grounds for double (which it is, imho),  
  
  That's platform specific.  Integral types in C++ are not  
guaranteed to be any specific size.  In practice, on modern  
systems long is either 32 bits or 64 bits.  IEEE double  
precision has a 53-bit mantissa, so it might be enough.  
  
> but you're right, one had to choose an integral type which has the same amount of bits as the corresponding real type has - is there some big_integral<bits> type in Boost as well?  
>   
  
  There is.  There's also uint_t<bits>::fast (or least).  
Neither one is sufficient by itself, and that still leaves  
the issue of types whose precision is not fixed at  
compile time.  
  
In Christ,  
Steven Watanabe

---

## Comment 4

> Username: DiscreteLogarithm  
> Created at: 2018-10-27 09:33:47 UTC  
> Updated at: 2018-10-27 09:34:21 UTC  
> Url: https://github.com/boostorg/random/issues/44#issuecomment-433605810  

@drauch   
> The current implementation fails to produce more different values than the range of the Engine is able to give.  
  
The actual number of different real values produced is much less than the number of different integer values fed into the `uniform_01` generator. For example, when you divide a 32-bit integer by 2<sup>32</sup>, the spacing between consecutive numbers is 2<sup>-32</sup>, while the spacing between consecutive 32-bit single precision float numbers in [0.5, 1) is 2<sup>-24</sup>. So for numbers in [0.5, 1), every 2<sup>8</sup> different integer values are rounded to only one float value. Considering these rounding effects only 10 &times; 2<sup>23</sup> different real values are produced.  
  
That being said, the number of different values of a 32-bit single precision float number in [0, 1) is also much less than 2<sup>32</sup> (obviously it should be able to represent negative numbers and numbers greater than 1 as well). The number of representable single precision float numbers in [0, 1) is 127 &times; 2<sup>23</sup>. But the common algorithm of dividing a 32-bit integer by 2<sup>32</sup> can not even produce all these 127 &times; 2<sup>23</sup> values because it can not produce numbers less than 2<sup>-32</sup> and it can not achieve the precision of single precision floats in [2<sup>-32</sup>, 2<sup>-9</sup>).  
  
In summary the common algorithm has two problems: it wastes the entropy of the Engine for numbers near 1 and it fails to produce all representable floating-point numbers near 0. Trying to relieve one problem by changing the size of the integer only exacerbate the other. Using a bigger integer type increases the waste and usually will not fully resolve the problem of not producing all representable float values. To represent all single precision floating-point numbers, 149-bit random integers are needed. To do the same for double precision floating-point numbers requires 1074-bit random integers.  
  
An efficient algorithm should only uses enough bits for the production of the mantissa (23 in the case of single precision) and use the rest towards the production of a geometric variate which would be used as the exponent. The exponent is a geometric variate with p=0.5 because the probability of a uniform real being in [0.5, 1) is 0.5, the probability of being in [0.25, 0.5) is 0.25, in [0.125, 0.25) it is 0.125 and so on. Details of such an algorithm is described in section 4 of [this preprint](https://arxiv.org/pdf/1810.04744.pdf). There's also an [implementation](https://github.com/DiscreteLogarithm/canonical-random-float) of this algorithm. The implementation uses templates and traits of `std::numeric_limit` class and does not assume any specific size for the result_type of the Engine or the float_type requested.  
  
> - generate_canonical allows you to specify the number of bits that you want.  
  
Actually the standard says that the number of bits generated is the lesser of requested bits and numeric_limits<RealType>::digits so it does not solve the problem  
  
> template<class RealType, size_t bits, class URBG>  
> RealType generate_canonical(URBG& g);  
> Complexity: Exactly k = max(1, ⌈b/ log<sub>2</sub> R⌉) invocations of g, where b is the lesser of numeric_limits<RealType>::digits and bits, and R is the value of g.max() − g.min() + 1.  
  
So for example in the case of single precision float, specifying any number of bits greater than 23 won't make a difference.  
  
PS: At the time of writing issue #47 , I haven't carefully read this one and I opened a new one. I'm sorry if that decision was wrong.

---

## Comment 5

> Username: drauch  
> Created at: 2018-10-28 16:08:43 UTC  
> Url: https://github.com/boostorg/random/issues/44#issuecomment-433718656  

If you generate the exponent and mantissa separately, how do you fix the "within the range min/max" issue? E.g., in this scenario:  
  
min = mantissa all 1, exponent 1025  
max = mantissa all 0, exponent 1027  
  
You need to generate 1025/1027 with a probability greater than 0 but very very low (1 / number of existing mantissas + 2).

---

## Comment 6

> Username: DiscreteLogarithm  
> Created at: 2018-10-28 20:31:27 UTC  
> Url: https://github.com/boostorg/random/issues/44#issuecomment-433738786  

Actually it's not similar to uniform_real_distribution in the sense that it does not generate uniform variates with a given min and max. It only generates numbers in [0, 1). One can multiply the result by max-min and then add min, to get numbers in [min, max).  
  
It may not produce all representable floating-point values in [min, max) with the correct probability due to possible rounding errors but it would perform much better than the common algorithm which produces only a fraction of representable floating-point values before multiplying the result by max-min and adding min.
