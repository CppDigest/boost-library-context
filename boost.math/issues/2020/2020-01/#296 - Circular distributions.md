# #296 - Circular distributions [Open]

> Username: ghost  
> Created at: 2020-01-06 13:44:47 UTC  
> Updated at: 2020-03-24 18:06:02 UTC  
> Url: https://github.com/boostorg/math/issues/296  

May math knowledge is quite limited. But in the past, I more than once had to deal with circular data. I usually use a Von Mises distribution to approximate my data. Having support in Boost.Math would have been very helpful back then.

---

## Comment 1

> Username: NAThompson  
> Created at: 2020-01-06 14:00:16 UTC  
> Url: https://github.com/boostorg/math/issues/296#issuecomment-571148034  

Not an unreasonable request. Looks like all the building blocks are already in Boost.Math. Got a number of other things to do at the moment, but I'll try to get this added as time permits.

---

## Comment 2

> Username: ghost  
> Created at: 2020-01-07 07:45:50 UTC  
> Url: https://github.com/boostorg/math/issues/296#issuecomment-571474596  

Is there anything I can do to assist you?

---

## Comment 3

> Username: NAThompson  
> Created at: 2020-01-07 13:48:38 UTC  
> Url: https://github.com/boostorg/math/issues/296#issuecomment-571593543  

@pcjmuenster : If you want to submit a PR I could help with the details. I'd probably just `cp normal.hpp von_mises.hpp` and just make the necessary adjustments to make it a Von Mises distribution.

---

## Comment 4

> Username: NAThompson  
> Created at: 2020-01-07 14:10:59 UTC  
> Updated at: 2020-01-07 16:01:36 UTC  
> Url: https://github.com/boostorg/math/issues/296#issuecomment-571602132  

Looking at the [Von Mises distribution wiki](https://en.wikipedia.org/wiki/Von_Mises_distribution), the mean, variance, mode, pdf and entropy are straightforward. The only one that would require significant testing is the CDF.  
  
Also a decision would have to be made about the `support()`, since `[-pi,pi]` is just as good as (say) `[0, 2pi]`.  
  
In fact this is a good first task for anyone who wants to learn how to contribute to the library.

---

## Comment 5

> Username: ghost  
> Created at: 2020-01-07 16:53:36 UTC  
> Url: https://github.com/boostorg/math/issues/296#issuecomment-571674013  

@NAThompson Thanks, I will try to look into it this weekend.

---

## Comment 6

> Username: ghost  
> Created at: 2020-01-09 09:02:35 UTC  
> Url: https://github.com/boostorg/math/issues/296#issuecomment-572460674  

Notes in case of my unforeseen death:   
For large values of kappa, the naive computation of the PDF fails since both exp(kappa) and bessel_i0(kappa) become quite large. For instance with kappa=100, the values exceed the range of single precision floating point numbers. However the maximum of such a distribution is still a fairly small number (cf. [Wolfram Alpha](https://www.wolframalpha.com/input/?i=von+mises+distribution+for+mu%3D0%2Ckappa%3D100%2Cx%3D0)).   
In a first attempt, I tried to approximate the logarithm of bessel_i0(kappa) instead and exponentiate the difference. I used the [Hankel Expansions](https://dlmf.nist.gov/10.40) for this. Boost currently uses different sets of polynomials to approximate the Bessel functions but doesn't yet include functions to directly retrieve their logarithms. For large kappa these could be trivially adapted from the polynomial approximations.  
Suppose we use a similar a approach to calculate the variance. The quotient of bessel_i1 and bessel_i0 converges fast to 1. This could lead to a loss of precision when we substract the result from 1. I have yet to figure out how severe that is. (A simple test showed that under the assumption we could compute the logarithms of the Bessel functions exactly – which we can not – the error is 0.05% from the true variance.)

---

## Comment 7

> Username: NAThompson  
> Created at: 2020-01-09 13:27:20 UTC  
> Updated at: 2020-01-09 13:33:24 UTC  
> Url: https://github.com/boostorg/math/issues/296#issuecomment-572560537  

@pcjmuenster : Could direct numerical quadrature work in the case where the series converges slowly? We have quite a few quadrature routines available, though some require expensive node-weight precomputation.  
  
Note that the general goal is 1ULP evaluation in arbitrary precision; see [here](https://blogs.mathworks.com/cleve/2017/01/23/ulps-plots-reveal-math-function-accurary/); you can create the plots using my garbage software [here](https://github.com/NAThompson/quicksvg).

---

## Comment 8

> Username: ghost  
> Created at: 2020-02-03 12:13:58 UTC  
> Url: https://github.com/boostorg/math/issues/296#issuecomment-581385757  

I have worked on the CDF yesterday and came across the following issue:  
The [literature](https://dl.acm.org/doi/10.1145/355744.355753) gives an algorithm that is also used in all other libraries I could find. The algorithm assumes the graph is centered around the mean. Thus the support would be `[mu-pi, mu+pi]`. I'd like to know if this kind of instance-dependent – in contrast to class-dependent – beahvior is allowed according to the contract of `support`. The alternative is to shift the tails around to fit them into the "standard" interval. That way the CDF would not be symmetric anymore.   
This also affects the implementation of `quantile`. I can't see any reasonable definition for "the lowest X%".

---

## Comment 9

> Username: NAThompson  
> Created at: 2020-02-03 14:51:41 UTC  
> Url: https://github.com/boostorg/math/issues/296#issuecomment-581450977  

@pcjmuenster : My opinion, such as it is, is that as long as it is documented, it's fine to have the support be `[mu-pi, mu+pi]`.  
  
@jzmaddock, @pabristow : What's your opinion?

---

## Comment 10

> Username: pabristow  
> Created at: 2020-02-03 16:13:15 UTC  
> Url: https://github.com/boostorg/math/issues/296#issuecomment-581490928  

Glad you are yet to meet an unforeseen premature demise ;-)    
  
 [mu-pi, mu+pi] looks fine by me - it only vital to do  what it says on the tin.  
  
I don't think we have been very precise what 'support' is anyway?

---

## Comment 11

> Username: NAThompson  
> Created at: 2020-02-03 16:52:54 UTC  
> Url: https://github.com/boostorg/math/issues/296#issuecomment-581509940  

Yeah, for periodic functions, `support()` is a bit ambiguous. Maybe it should be the whole real line?

---

## Comment 12

> Username: ghost  
> Created at: 2020-02-03 17:26:02 UTC  
> Url: https://github.com/boostorg/math/issues/296#issuecomment-581524790  

Independent of the return value of `support`, a decision has to be made where `cdf` shall return `0` and `1`, this also impacts `quantile` and `median`. The latter would (kind of) reasonably be equal to `mean` like the "normal" normal distribution. Otherwise, somebody has to find a formula for skewness and kurtosis cause I am totally out of my field then …   
`range`, i. e. the domain of `pdf`, can naturally be the set of real numbers. But `support` … that would be weird. OTOH: being able to calculate the PDF but not the CDF for angles outside of `[mu - pi, mu + pi]` is also counter-intuitive. In the end, I think it would make even more sense if one had to ask directly for an interval of angles instead of substracting to CDFs. Then some magical formula would provide the area of the circular segment between them (counter-clockwise).

---

## Comment 13

> Username: NAThompson  
> Created at: 2020-02-03 17:36:45 UTC  
> Url: https://github.com/boostorg/math/issues/296#issuecomment-581529380  

@pcjmuenster : I think this will require writing very careful error messages that self-document the requirements of the function.  
  
I might start with the definition `support() := [µ-π, µ+π)`. Then `cdf(µ-π) := 0`, and `cdf(µ+π) := 1`. Don't allow `pdf` to be evaluated anywhere, throw when `x \not \in [µ-π, µ+π]`.  
  
Also, should µ be constrained?

---

## Comment 14

> Username: ghost  
> Created at: 2020-03-24 16:27:06 UTC  
> Url: https://github.com/boostorg/math/issues/296#issuecomment-603346844  

I tried every trick I know (not many) to increase the precision. But even then most functions don't work for distributions with `kappa > 10`. Maybe somebody else is able to complete the work.

---

## Comment 15

> Username: pabristow  
> Created at: 2020-03-24 16:41:01 UTC  
> Url: https://github.com/boostorg/math/issues/296#issuecomment-603355965  

@jzmaddock is a man who knows a very lot of precision tricks.  
  
https://en.wikipedia.org/wiki/Von_Mises_distribution says  
  
"as {\displaystyle \kappa }\kappa  increases, the distribution approaches a normal distribution in x  with mean μ and variance 1/{\displaystyle \kappa }\kappa "  
  
So would normal do at some (computable) cross-over point?   
  
Or be close enough approximation that some 'adjustment' could be made?

---

## Comment 16

> Username: NAThompson  
> Created at: 2020-03-24 18:06:01 UTC  
> Url: https://github.com/boostorg/math/issues/296#issuecomment-603416145  

@pcjmuenster : Open a PR and we can see what you're doing.
