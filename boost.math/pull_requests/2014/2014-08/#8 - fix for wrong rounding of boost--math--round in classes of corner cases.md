# #8 fix for wrong rounding of boost::math::round in classes of corner cases [Merged]

> Username: robin-zimmeck  
> Created at: 2014-08-24 10:00:34 UTC  
> Updated at: 2014-08-25 16:06:46 UTC  
> Merged at: 2014-08-25 16:05:10 UTC  
> Closed at: 2014-08-25 16:05:10 UTC  
> Url: https://github.com/boostorg/math/pull/8  

This pull request fixes two classes of rounding bugs of `boost::math::round` occuring in two corner cases.  
  
The first corner case are the direct predecessor/successors of 0.5/-0.5. For `float` and `double` types these are the following numbers:  
- -0.4999999701976776123046875f  
- 0.4999999701976776123046875f  
- -0.499999999999999944488848768742172978818416595458984375  
- 0.499999999999999944488848768742172978818416595458984375  
  
These numbers are nearer to zero than -0.5/0.5 and must be rounded to zero according to the documentation of `boost::math::round`, but are rounded to -1.0/1.0.  
  
To explain this, lets assume an imaginary floating-point type resembling IEEE 754 types with a four-bit mantissa (the exponent width is not important for this). As the positive and negative cases are symmetric, lets only look at the positive case. The significant expression of the original implementation to get the away-from-zero rounded number for `v` for this case is:  
  
```  
floor(v + 0.5f)  
```  
  
0.5 is represented in binary as <code>0.1<b>0000</b><sub>b</sub></code> (the `1` is implicit and there are four `0` bits in the mantissa). The direct predecessor (the largest representable number that is smaller than 0.5) is <code>0.01<b>1111</b><sub>b</sub></code> = 0.484375. The original implementation now adds 0.5 to this number:  
  
<pre>0.01<b>1111</b><sub>b</sub> + 0.1<b>0000</b><sub>b</sub> = 0.1<b>1111</b><i>1</i><sub>b</sub></pre>  
  
  
The result is too wide for the mantissa of the data type and is rounded by the precessor to <code>1.<b>0000</b><sub>b</sub></code> (actually, this is dependent on the rounding flags). Taking the floor from this number yields 1.0 as the result instead of the correct 0.0.  
  
The second corner case are numbers of the format <code>v = +/-1<b>xyz1</b><sub>b</sub></code> for arbitrary `x`, `y` and `z`. In this case, a similar rounding problem occurs:  
  
<pre>1<b>xyz1</b><sub>b</sub> + 0.1<b>0000</b><sub>b</sub> = 1<b>xyz1</b>.<i>1</i><sub>b</sub></pre>  
  
  
Again, the last digit is not representable anymore and the processor can round this up `v + 1` (`v - 1` for negative `v`), yielding that as the result instead of `v`. This is especially problematic, as an already "round" (integral) number is rounded to another integral number.  
  
This pull request fixes the two corner cases by handling the the numbers `-0.5 < v < 0.5` as a special case to fix the first cornercase and by changing the calculation order for the second corner case. For positive `v`, the significant expression now is:  
  
```  
result_type c(ceil(v));  
return 0.5 < c - v ? c - 1 : c;  
```  
  
This calulcates `ceil(v)` first, avoiding any rounding as `ceil` doesn't need to round and `v` is the raw input value. For all integral `v`, `ceil` yields exactly `v`, so the substraction result is zero without any rounding. For non-integral `v` the substraction is alwaysexact and representable in the data type as the value of the result is always smaller than `v`. This guarantees that the comparison with 0.5 is correct. The substraction of 1 in the rounding-down raises no problem, as it is either exact or truncated if the precision of `v` can't represent 1 anymore.  
  
For negative `v`, the implementation/explanation is symmetric.  
  
The error probably won't occur on the x86 32 bit platform not using SSE, but using extended precision in the 387 FPU registers, I haven't tested this. I also didn't test the probable performance penalty of the correction (especially in a real-world application). Maybe there also exists a better fix than in this pull request (I didn't yet come up with one). As I think correctness (even in such admittedly (probably) rare corner cases on the edge of the precision) is more important than performance in most applications, a slightly slower implementation that is correct should be preferred.  
  
I ran a (C++11 only) successfull test comparing `std::round` against `boost::math::round` for the complete finite value space of `float`. I did not include this, as it takes a long time to run, but is is in the `roundbug-full-scan` branch of my fork of the boost.math repository.  
  
I did not look into non IEEE 754 floating point number representation, but I think that the implementation should not induce errors there.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2014-08-25 16:06:46 UTC  
> Url: https://github.com/boostorg/math/pull/8#issuecomment-53285047  

Many thanks for tracking this down and fixing it!  
  
John.

---
