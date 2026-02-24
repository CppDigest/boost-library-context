# #234 Jacobi polynomials [Merged]

> Username: NAThompson  
> Created at: 2019-08-05 12:43:43 UTC  
> Updated at: 2019-10-17 21:46:26 UTC  
> Merged at: 2019-08-29 12:43:21 UTC  
> Closed at: 2019-08-29 12:43:21 UTC  
> Url: https://github.com/boostorg/math/pull/234  



---

## Comment 1

> Username: NAThompson  
> Created_at: 2019-08-05 12:54:31 UTC  
> Url: https://github.com/boostorg/math/pull/234#issuecomment-518222306  

@jzmaddock : Did you have a nice tool for gamma function ratios such as are seen in the [Jacobi polynomial derivatives](https://en.wikipedia.org/wiki/Jacobi_polynomials)? For some reason I thought you had such a thing . . .

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2019-08-05 14:22:33 UTC  
> Url: https://github.com/boostorg/math/pull/234#issuecomment-518255203  

>@jzmaddock : Did you have a nice tool for gamma function ratios such as are seen in the Jacobi polynomial derivatives? For some reason I thought you had such a thing . . .  
  
Yep, several such tools.  Assuming k is an integer, then that's a rising factorial (aka pochhammer), see https://www.boost.org/doc/libs/1_70_0/libs/math/doc/html/math_toolkit/factorials/sf_rising_factorial.html.  
  
In the general case, we have https://www.boost.org/doc/libs/1_70_0/libs/math/doc/html/math_toolkit/sf_gamma/gamma_ratios.html

---

## Comment 3

> Username: NAThompson  
> Created_at: 2019-08-06 16:48:50 UTC  
> Url: https://github.com/boostorg/math/pull/234#issuecomment-518752023  

> Assuming k is an integer, then that's a rising factorial (aka pochhammer)  
  
The division by 2^k means I get some overflow resistance by doing the rising factorial by hand. Hopefully there aren't any cool tricks for stable evaluations of Pochhammers that I lost . . .

---
