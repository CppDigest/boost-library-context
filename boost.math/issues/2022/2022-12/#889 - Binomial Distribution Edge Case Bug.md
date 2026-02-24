# #889 - Binomial Distribution Edge Case Bug [Closed]

> Username: mborland  
> Created at: 2022-12-03 17:26:51 UTC  
> Updated at: 2022-12-04 14:56:02 UTC  
> Closed at: 2022-12-04 14:56:02 UTC  
> Url: https://github.com/boostorg/math/issues/889  

See: https://github.com/scipy/scipy/issues/17388#issuecomment-1336112873  
  
From the OP:  
```  
print(binom.ppf(q = 0.0, n = 4, p = 0.5)) # prints -1.0 (but should be 0.0?)  
print(binom.ppf(q = 0.3, n = 4, p = 1.0)) # prints 0.0 (but should be 4.0?)  
```  
  
For the first case we return 0.0 which is the correct behavior, but SciPy [has it's own convention](https://github.com/scipy/scipy/pull/15967#issuecomment-1095817726). For the second case we are incorrect as [confirmed by WolframAlpha ](https://www.wolframalpha.com/input?i=quantile%28binomial+distribution%284%2C1%29%2C0.3%29).

---

## Comment 1

> Username: NAThompson  
> Created at: 2022-12-03 23:05:26 UTC  
> Url: https://github.com/boostorg/math/issues/889#issuecomment-1336274955  

> For the first case we return 0.0 which is the correct behavior, but SciPy https://github.com/scipy/scipy/pull/15967#issuecomment-1095817726.  
  
Wait, if we have the correct behavior, then there can be no convention that makes it incorrect???  
  
On the other hand, we can follow our own documented convention . . .

---

## Comment 2

> Username: mborland  
> Created at: 2022-12-03 23:33:34 UTC  
> Url: https://github.com/boostorg/math/issues/889#issuecomment-1336278415  

> > For the first case we return 0.0 which is the correct behavior, but SciPy https://github.com/scipy/scipy/pull/15967#issuecomment-1095817726.  
>   
>   
>   
> Wait, if we have the correct behavior, then there can be no convention that makes it incorrect???  
>   
>   
>   
> On the other hand, we can follow our own documented convention . . .  
  
The linked PR only changes the behavior for the second case which is inline with our documentation and expected values from wolframalpha.

---

## Comment 3

> Username: NAThompson  
> Created at: 2022-12-03 23:47:45 UTC  
> Url: https://github.com/boostorg/math/issues/889#issuecomment-1336280049  

Ok, I see; so is your contention that scipy's convention in the first case is not a sensible way of assigning the singular limit?

---

## Comment 4

> Username: mborland  
> Created at: 2022-12-03 23:50:51 UTC  
> Url: https://github.com/boostorg/math/issues/889#issuecomment-1336280392  

> Ok, I see; so is your contention that scipy's convention in the first case is not a sensible way of assigning the singular limit?  
  
Yes, and if we did change the way we handled that case it would break their code.
