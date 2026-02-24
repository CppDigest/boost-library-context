# #45 Polynomial: fixes for Stein gcd [Open]

> Username: jeremy-murphy  
> Created at: 2016-06-26 03:21:12 UTC  
> Updated at: 2022-10-29 17:13:15 UTC  
> Url: https://github.com/boostorg/math/pull/45  

**Still slightly a work in progress...**  
  
Primarily, this PR is to fill in the missing gaps for making Stein gcd actually support polynomials.  
  
However, in working on it, I realized that there is an awkward expectation of gcd, which Stepanov actually rails against in [this video](http://www.stepanovpapers.com/stepanov_gcd.mov) starting at 1:22:07. And although I am inclined to agree with him, he is arguing _against_ Knuth, so I don't expect everyone to jump on board easily.  
Essentially, he argues that, for example, `gcd(-6, -9) == -3` is perfectly acceptable, rather than requiring it to return the absolute value, because, the requirement of the greatest common divisor of _u_ and _v_ is that it be divisible by all other common divisors of _u_ and _v_, which according to that requirement, multiplication by a unit (-1) is irrelevant.  
This becomes particularly apparent with polynomials, where the sign and thus absolute value is... what? A convention at best. And what about magnitude? Is `4x + 2 > 2x + 1`? Again, only by lexicographical convention; for divisibility of F[x], `u(x) == c·u(x)`.  
With Gaussian integers it becomes even harder to judge which divisor would be greater by magnitude when you have three units, 1, -1 and _i_.  
So, my sense is that gcd has been defined in terms of integers for so long that the assumption of total ordering and thus providing "greatest" not just in terms of divisibility but also in terms of positive magnitude has become all melded together. But really, there are two separate things going on: firstly, the true gcd computation, and secondly, normalization to a unique value.  
I think we have to stick with the normalization because convention demands it, but it's mathematically icky when we start making gcd generic. I suspect we will find cases where the gcd is defined but the normalization is not. It will be interesting. :)  
  
And another thing! Although this implementation appears to be correct, the inherent imprecision of floating point is a major factor affecting useful operation of this algorithm. So although simple textbook examples with nice round divisors that are multiples of 2 work like a charm, anything less than ideal can end in completely bogus results.  
An alternative implementation of the crucial `subtract` function--from Algorithmic Cryptanalysis by Antoine Joux--uses multiplication and no division, so avoids the imprecision of fractions, but instead grows coefficients to enormous size, returning values 8 (decimal) orders of magnitude larger than expected. Now the interesting thing about that is that because there is no division of coefficients any more, this also works for integer coefficients. But aside from that, we can treat these huge floating point coefficients as integers and normalize them by dividing through by their gcd using `gcd_range`.  
I think this could actually be a solution...

---

## Comment 1

> Username: jeremy-murphy  
> Created_at: 2016-07-03 12:13:02 UTC  
> Url: https://github.com/boostorg/math/pull/45#issuecomment-230150364  

Good news is that I think I've perfected Stein gcd for Z[_x_]. It's just R[_x_] that remains tricky.

---

## Comment 2

> Username: jeremy-murphy  
> Created_at: 2016-12-05 12:48:56 UTC  
> Url: https://github.com/boostorg/math/pull/45#issuecomment-264845915  

After not looking at this for months, I've come back to it and worked through some issues.  
  
What it has left though is a lingering question as to why the Euclidean algorithm does not work consistently for R[_x_].  
  
And, the need for computing the gcd of floating point numbers, using std::fmod in place of operator%.  
That, however, sounds like it deserves to be a PR of its own.

---

## Comment 3

> Username: jeremy-murphy  
> Created_at: 2018-11-22 10:51:40 UTC  
> Url: https://github.com/boostorg/math/pull/45#issuecomment-440991750  

Oh dear, I kind of did forget about this.  :(

---
