# #546 - double exponential docs incorrect [Closed]

> Username: jzmaddock  
> Created at: 2021-02-21 16:49:09 UTC  
> Updated at: 2021-02-23 02:06:33 UTC  
> Closed at: 2021-02-22 14:01:17 UTC  
> Url: https://github.com/boostorg/math/issues/546  

Trivial issue:  
  
the docs have the namespace as `boost::math` rather than `boost::math::quadrature`.  It would be good to mention the namespace in each integrators sub-page as well as the overview.  
  
I've also just fallen into a trap, in that:  
  
`my_exp_sinh.integrate(f, a)`  
  
Does not integrate from a to infinity, but treats `a` as an epsilon tolerance.  Not sure what we can do about that though.

---

## Comment 1

> Username: NAThompson  
> Created at: 2021-02-21 16:54:01 UTC  
> Url: https://github.com/boostorg/math/issues/546#issuecomment-782888049  

First issue is fixed; double_exponential.qbk was the only file with that issue.  
  
> Does not integrate from a to infinity, but treats a as an epsilon tolerance. Not sure what we can do about that though.  
  
IIRC the domain of `exp_sinh` is *always* [-inf, inf].  
  
How about `if (abs(a) > 1) { std::cerr << "Did you mistake a domain for a tolerance?" }`

---

## Comment 2

> Username: jzmaddock  
> Created at: 2021-02-21 17:46:08 UTC  
> Url: https://github.com/boostorg/math/issues/546#issuecomment-782895926  

Thanks Nick!  
  
>IIRC the domain of exp_sinh is always [-inf, inf].  
  
[0, inf]  ;)  
  
> How about if (abs(a) > 1) { std::cerr << "Did you mistake a domain for a tolerance?" }  
  
Make that a `domain_error` and a check on the error tolerance, and yes, that seems sensible.  
  
BTW we seem not to have documented anywhere whether the error tolerance is absolute or relative, likewise the error returned.  L1 also need documenting, even though that one is reasonably obvious :)

---

## Comment 3

> Username: NAThompson  
> Created at: 2021-02-21 22:56:42 UTC  
> Updated at: 2021-02-21 22:59:01 UTC  
> Url: https://github.com/boostorg/math/issues/546#issuecomment-782943655  

I'm trying to remember why I didn't support `[a, inf]` domains for finite `a` . . .  maybe I thought the translation was too trivial to bother with?  
  
In any case looks like I can't change it now!

---

## Comment 4

> Username: jzmaddock  
> Created at: 2021-02-22 11:39:54 UTC  
> Url: https://github.com/boostorg/math/issues/546#issuecomment-783313759  

>I'm trying to remember why I didn't support [a, inf] domains for finite a . . . maybe I thought the translation was too trivial to bother with?  
  
It *is* supported, but you have to supply both ends of the range, the trap I fell into, was assuming that if I only supplied one end the other would default to +inf, but then it gets mixed up with the other constructor overload so we can't do that :(  
  
No matter.

---

## Comment 5

> Username: NAThompson  
> Created at: 2021-02-22 14:01:17 UTC  
> Updated at: 2021-02-22 14:01:34 UTC  
> Url: https://github.com/boostorg/math/issues/546#issuecomment-783396384  

Well I should've put more thought into the design here, but I believe this ship has sailed as it's not worth a backwards incompatible change . . .  
  
Your assumption is very reasonable . . .

---

## Comment 6

> Username: NAThompson  
> Created at: 2021-02-23 02:04:12 UTC  
> Updated at: 2021-02-23 02:06:33 UTC  
> Url: https://github.com/boostorg/math/issues/546#issuecomment-783810608  

> BTW we seem not to have documented anywhere whether the error tolerance is absolute or relative, likewise the error returned. L1 also need documenting, even though that one is reasonably obvious :)  
  
It looks like I did it in text rather than an equation:  
  
"The routine stops when successive estimates of the integral I1 and I0 differ by less than the tolerance multiplied by the estimated L1 norm of the function."  
  
This is a scale-invariant tolerance, but not quite what people think when they ask for relative error.  
  
It's repeated in it's own section:  
  
https://www.boost.org/doc/libs/1_75_0/libs/math/doc/html/math_toolkit/double_exponential/de_tol.html
