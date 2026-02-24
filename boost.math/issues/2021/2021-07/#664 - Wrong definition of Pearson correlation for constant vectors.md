# #664 - Wrong definition of Pearson correlation for constant vectors [Closed]

> Username: zbjornson  
> Created at: 2021-07-19 23:04:08 UTC  
> Updated at: 2022-02-15 16:20:33 UTC  
> Closed at: 2022-02-15 16:20:33 UTC  
> Url: https://github.com/boostorg/math/issues/664  

The `correlation_coefficient` function has two special cases:  
  
https://github.com/boostorg/math/blob/4cdc6450d5844b6dcd787f70ca4122fd66d4b855/include/boost/math/tools/bivariate_statistics.hpp#L75-L84  
  
The correlation is undefined if either stddev is 0 however, so I think that should just be `if (Qu == 0 || Qv == 0) return 0./0.` (or similar). The current behavior is documented, but returning 0 and 1 make these states impossible to distinguish from true 0 or 1 correlation coefficients without an additional loop in the consumer code.  
  
References:  
* https://stats.stackexchange.com/questions/23676/normalized-correlation-with-a-constant-vector  
* Mathematica and R both print a warning and don't evaluate the input.

---

## Comment 1

> Username: NAThompson  
> Created at: 2021-07-20 01:59:37 UTC  
> Url: https://github.com/boostorg/math/issues/664#issuecomment-882989348  

Think `return std::numeric_limits<Real>::quiet_NaN()` would be preferable?

---

## Comment 2

> Username: zbjornson  
> Created at: 2021-07-20 02:03:37 UTC  
> Url: https://github.com/boostorg/math/issues/664#issuecomment-882992309  

Yeah, I think that's preferable.

---

## Comment 3

> Username: jeremy-murphy  
> Created at: 2021-07-20 11:57:34 UTC  
> Url: https://github.com/boostorg/math/issues/664#issuecomment-883333358  

I agree that it's _preferable_, but have you weighed up the pros and cons of other options? I mean, such as throwing an exception or returning an `optional`? I ask this because ordinary floating point arithmetic might cause a qNaN due to over/under flow right? And now this special case is being lumped in with them, even though the algorithm knows the difference between them. Maybe it's fine, but, maybe not.

---

## Comment 4

> Username: zbjornson  
> Created at: 2021-07-20 17:00:01 UTC  
> Url: https://github.com/boostorg/math/issues/664#issuecomment-883549234  

TBH I don't know what's allowed or idiomatic in Boost. `optional` or throwing an exception seem reasonable as well, and are indeed clearer.  
  
> ordinary floating point arithmetic might cause a qNaN due to over/under flow right?  
  
I think this algorithm is very difficult to get to under/overflow, fwiw. (The code attributes Sandia Labs, but as far as I can tell both the mean and variance are Welford's 1962 methods, which have been extensively analyzed. Although not consistently found to be "good" in terms of precision, I've yet to see under/overflow come up as a criticism.)  
  
But over/underflow will never generate a NaN as far as I know. If FTZ/DAZ are set, underflow would be 0; otherwise +/-2<sup>e<sub>min</sub></sup> or a denormal. Overflow goes to infinity or the largest finite number depending on rounding mode.

---

## Comment 5

> Username: jzmaddock  
> Created at: 2021-07-20 17:39:42 UTC  
> Url: https://github.com/boostorg/math/issues/664#issuecomment-883573999  

>I agree that it's preferable, but have you weighed up the pros and cons of other options? I mean, such as throwing an exception or returning an optional? I ask this because ordinary floating point arithmetic might cause a qNaN due to over/under flow right? And now this special case is being lumped in with them, even though the algorithm knows the difference between them. Maybe it's fine, but, maybe not.  
  
I was just about to ask if this should be considered a domain error?

---

## Comment 6

> Username: NAThompson  
> Created at: 2021-07-20 20:18:23 UTC  
> Url: https://github.com/boostorg/math/issues/664#issuecomment-883667768  

> I ask this because ordinary floating point arithmetic might cause a qNaN due to over/under flow right?  
  
The algorithm used to compute the mean and variance is extremely hard to overflow or underflow. In fact IIRC it's impossible.  
  
> I was just about to ask if this should be considered a domain error?  
  
Personally I feel like a NaN is the correct choice here; I prefer to leave throws to issues that could be caught in testing.

---

## Comment 7

> Username: jeremy-murphy  
> Created at: 2021-07-20 23:31:34 UTC  
> Url: https://github.com/boostorg/math/issues/664#issuecomment-883772061  

> I think this algorithm is very difficult to get to under/overflow, fwiw. (The code attributes Sandia Labs, but as far as I can tell both the mean and variance are Welford's 1962 methods, which have been extensively analyzed. Although not consistently found to be "good" in terms of precision, I've yet to see under/overflow come up as a criticism.)  
>   
> But over/underflow will never generate a NaN as far as I know. If FTZ/DAZ are set, underflow would be 0; otherwise +/-2emin or a denormal. Overflow goes to infinity or the largest finite number depending on rounding mode.  
  
Yes, sorry, I didn't get that quite right.  :)  Overflow will give infinity, and then certain operations on infinity will cause a NaN (https://www.doc.ic.ac.uk/~eedwards/compsys/float/nan.html). Point is, you can get a NaN just from doing arithmetic.  
  
The reason I think an exception might be preferable is that in this case, we logically know that the result is invalid, as opposed to having calculated an invalid result by accident. It's like a precondition of the function was not met, but we only discovered it at the end.

---

## Comment 8

> Username: NAThompson  
> Created at: 2021-07-20 23:55:39 UTC  
> Url: https://github.com/boostorg/math/issues/664#issuecomment-883779688  

Note that going to an `optional` is a breaking change; I'm not sure I want to pull the trigger on that just for a case that has probability zero.

---

## Comment 9

> Username: jeremy-murphy  
> Created at: 2021-07-21 13:52:14 UTC  
> Url: https://github.com/boostorg/math/issues/664#issuecomment-884207136  

It would break in a _good_ way. Users would have to change their code and consider the possible error result.  
  
Are we really at the point of arguing whether a NaN is a possible result simply from valid input? Look at this code:  
```  
        Qu = Qu + (i*u_tmp*u_tmp)/(i+1);  
        Qv = Qv + (i*v_tmp*v_tmp)/(i+1);  
```  
`u_tmp` or `v_tmp` simply have to be large enough that squaring them causes overflow, which will result in an `inf` value, which will then be evaluated here:  
`    Real rho = cov/sqrt(Qu*Qv);  
`  
and turn into a NaN. It's not hard.  
  
Or, there just might be an `inf` value in the input. And, that will become a `NaN` very quickly since our implementation doesn't check for or guard against it.  
  
So, I think we can do better than a NaN, since that is already returned for (at least) two scenarios.

---

## Comment 10

> Username: jeremy-murphy  
> Created at: 2021-07-27 01:57:23 UTC  
> Url: https://github.com/boostorg/math/issues/664#issuecomment-887148463  

@NAThompson , what's going on here? Three people have expressed that something other than returning NaN might be better but you've merged your personal preference without, it seems, any rational justification for it? I know that working with people is harder than programming, but Boost is a community; shutting down discussion is not a healthy way to foster it. We're all trying to help here and there is no pressure to merge changes quickly.

---

## Comment 11

> Username: NAThompson  
> Created at: 2021-07-27 02:45:18 UTC  
> Updated at: 2021-07-27 02:45:51 UTC  
> Url: https://github.com/boostorg/math/issues/664#issuecomment-887164689  

Ok, issue is reopened. We can start a PR which makes any of these changes.

---

## Comment 12

> Username: HDembinski  
> Created at: 2022-02-15 09:04:58 UTC  
> Url: https://github.com/boostorg/math/issues/664#issuecomment-1040024676  

I just stumbled over this, so let me balance the scales a bit. I agree with @NAThompson that return quient_NaN is appropriate.  
  
As a general rule, low-level math code that operates on floating point numbers should not throw, because we have an elegant way to signal errors with floats and that is to return NaN. Unlike an exception, NaN does not allow you to pass on information about what caused the issue (strictly speaking you can put a payload into the NaN value, but that is not platform-independent AFAIU), but it has very low cost and it allows one to use the code in settings where exceptions are disabled in the compiler.  
  
It also makes this a non-breaking change, which is important for a library designer. Breaking changes have a high threshold, the benefit has to be so great as to outweigh the cost. This is not given here.
