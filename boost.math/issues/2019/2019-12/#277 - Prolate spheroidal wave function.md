# #277 - Prolate spheroidal wave function [Closed]

> Username: NAThompson  
> Created at: 2019-12-05 18:42:10 UTC  
> Updated at: 2020-01-13 17:05:35 UTC  
> Closed at: 2020-01-13 17:05:23 UTC  
> Url: https://github.com/boostorg/math/issues/277  

A perhaps nice addition to the suite of special functions might be the [prolate spheroidal wave functions](https://www.ntu.edu.sg/home/lilian/Review-PSWFs-final.pdf). These functions are the set of bandlimited functions whose norm is maximally concentrated in a finite time interval, and hence are useful for modeling attenuated signals.

---

## Comment 1

> Username: pabristow  
> Created at: 2019-12-06 09:48:36 UTC  
> Url: https://github.com/boostorg/math/issues/277#issuecomment-562506328  

Looks interesting ;-)  
  
I note that   
  
Algorithm 840: Computation of Grid Points,  
Quadrature Weights and Derivatives for Spectral Element Methods Using Prolate Spheroidal Wave Functions—Prolate Elements  
JOHN P. BOYD  
  
uses Matlab and 'eigen-stoff' but probably Boost.uBlas or Eigen would suffice?  
  
I've put it in my Possible TODO file :-)

---

## Comment 2

> Username: NAThompson  
> Created at: 2020-01-13 17:05:35 UTC  
> Url: https://github.com/boostorg/math/issues/277#issuecomment-573767418  

Closed as moved to wishlist.
