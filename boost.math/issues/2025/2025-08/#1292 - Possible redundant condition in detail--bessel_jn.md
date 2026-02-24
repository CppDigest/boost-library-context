# #1292 - Possible redundant condition in detail::bessel_jn [Closed]

> Username: matwey  
> Created at: 2025-08-07 08:28:41 UTC  
> Updated at: 2025-08-09 15:07:38 UTC  
> Closed at: 2025-08-09 15:07:38 UTC  
> Url: https://github.com/boostorg/math/issues/1292  

It seems that the following code has redundant condition or bug:  
  
https://github.com/boostorg/math/blob/c68d7463d73732bef065936e607dc45cc11920bb/include/boost/math/special_functions/detail/bessel_jn.hpp#L87  
  
`(x < 1) || (x < 5)` is equivalent to just `x < 5`. It is also worth to add a comment line if I wrongly interpret this confusing code.

---

## Comment 1

> Username: ckormanyos  
> Created at: 2025-08-07 08:42:06 UTC  
> Url: https://github.com/boostorg/math/issues/1292#issuecomment-3163124396  

Hi @matwey   
  
Good catch. I guess we need to figure out what is intended.  
  
What particularly catches my interest in that condition is the logical sequence of queries. Generally the queries are intended to select situations that call for small-argument Taylor-series expansion.  
  
I almost get the feeling that this might be intended as:  
  
```  
else if  
(  
     (x < 1)  
  || ((n > x * x / 4) && (x < 5))  
)  
```  
  
Cc: @mborland and @jzmaddock

---

## Comment 2

> Username: matwey  
> Created at: 2025-08-07 08:54:50 UTC  
> Url: https://github.com/boostorg/math/issues/1292#issuecomment-3163177388  

The same condition also appears at:  
https://github.com/boostorg/math/blob/c68d7463d73732bef065936e607dc45cc11920bb/include/boost/math/special_functions/detail/bessel_jy.hpp#L330

---

## Comment 3

> Username: ckormanyos  
> Created at: 2025-08-07 08:58:30 UTC  
> Url: https://github.com/boostorg/math/issues/1292#issuecomment-3163190918  

I think I will change both logic sequences to what I think might be right and run CI/CD sort of taking a shot in the dark.

---

## Comment 4

> Username: ckormanyos  
> Created at: 2025-08-07 09:03:58 UTC  
> Updated at: 2025-08-07 09:04:19 UTC  
> Url: https://github.com/boostorg/math/issues/1292#issuecomment-3163208470  

See also #1293 via commit f52df1cb9ee88421e7afee6754bc3675077880ca

---

## Comment 5

> Username: ckormanyos  
> Created at: 2025-08-07 09:05:41 UTC  
> Url: https://github.com/boostorg/math/issues/1292#issuecomment-3163213852  

We will need the review of @jzmaddock regardless of what this CI/CD run produces.  
  
Cc: @mborland and @matwey

---

## Comment 6

> Username: matwey  
> Created at: 2025-08-07 09:58:39 UTC  
> Url: https://github.com/boostorg/math/issues/1292#issuecomment-3163384153  

> I think I will change both logic sequences to what I think might be right and run CI/CD sort of taking a shot in the dark.  
  
Have you checked that this branch is really covered by any unit tests?

---

## Comment 7

> Username: ckormanyos  
> Created at: 2025-08-07 10:01:18 UTC  
> Updated at: 2025-08-07 10:02:12 UTC  
> Url: https://github.com/boostorg/math/issues/1292#issuecomment-3163392412  

> Have you checked that this branch is really covered by any unit tests?  
  
Hi @matwey you'ver read my mind.  
  
I will do that in the next step(s). After that we need to see if we need new test cases. Then we also need to consider the Multiprecision tests and their coverage of these lines.  
  
Stay tuned...

---

## Comment 8

> Username: jzmaddock  
> Created at: 2025-08-07 11:15:44 UTC  
> Url: https://github.com/boostorg/math/issues/1292#issuecomment-3163637644  

I think it's a typo: if you look at the history it began life as the simple `x < 1` condition, and it look like I forgot to remove that when I updated the condition, my bad :(  
  
So I think it should be `(n > x * x / 4) || (x < 5)`.  The line is covered, but we just need to double check that it's covered for `1 < x < 5`: it should be since I generally use our test data to fine tune these conditions (and add more when required).  
  
Good catch, and thanks for the report!!

---

## Comment 9

> Username: ckormanyos  
> Created at: 2025-08-07 11:41:47 UTC  
> Url: https://github.com/boostorg/math/issues/1292#issuecomment-3163768869  

> So I think it should be `(n > x * x / 4) || (x < 5)`. The line is covered, but we just need to double check that it's covered for $1 < x < 5$: it should be since I generally use our test data to fine tune these conditions (and add more when required).  
  
Thanks John (@jzmaddock). I'll update the PR and just to be sure add additional tests for this issue nonetheless.  
  
Cc: @matwey and @mborland

---

## Comment 10

> Username: jzmaddock  
> Created at: 2025-08-07 12:22:03 UTC  
> Url: https://github.com/boostorg/math/issues/1292#issuecomment-3163960770  

It's pretty well covered here: https://github.com/boostorg/math/blob/c68d7463d73732bef065936e607dc45cc11920bb/test/test_bessel_j.hpp#L250 although most of the tests trigger the `(n > x * x / 4)` condition, so if you add anything, some tests for small `n` and x near 5 would be the main corner case.

---

## Comment 11

> Username: jzmaddock  
> Created at: 2025-08-07 12:24:46 UTC  
> Url: https://github.com/boostorg/math/issues/1292#issuecomment-3163968453  

Actually, if you're adding ad-hoc test cases, can you please add them after here: https://github.com/boostorg/math/blob/c68d7463d73732bef065936e607dc45cc11920bb/test/test_bessel_j.hpp#L262 so all the test coverage ends up in the one file.  It makes maintenance a bit easier if there's just the one test to run to cover everything! :)

---

## Comment 12

> Username: ckormanyos  
> Created at: 2025-08-07 12:31:30 UTC  
> Url: https://github.com/boostorg/math/issues/1292#issuecomment-3163989251  

> Actually, if you're adding ad-hoc test cases, can you please add them after here  
  
Yes. Will do.
