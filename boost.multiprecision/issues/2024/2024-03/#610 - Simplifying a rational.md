# #610 - [Question] Simplifying a rational [Closed]

> Username: sloriot  
> Created at: 2024-03-14 14:15:45 UTC  
> Updated at: 2024-03-14 18:06:46 UTC  
> Closed at: 2024-03-14 18:06:45 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/610  

Hi,  
  
Is there a built-in method to simplify a rational? Calling gcd and doing the division of the numerator and denominator  is possible but while computing the gcd we should already have the simplified rational.  
  
Thanks.

---

## Comment 1

> Username: jzmaddock  
> Created at: 2024-03-14 18:06:45 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/610#issuecomment-1998042543  

The short answer is no, but I'm not quite sure what you're asking - for the ratio of two integers, the Euclid GCD algorithm doesn't compute the reduced terms just the common divisor.
