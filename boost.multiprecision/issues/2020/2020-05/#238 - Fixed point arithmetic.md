# #238 - Fixed point arithmetic [Closed]

> Username: NAThompson  
> Created at: 2020-05-22 01:30:19 UTC  
> Updated at: 2020-05-22 15:14:55 UTC  
> Closed at: 2020-05-22 15:14:54 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/238  

Are there any backends that support fixed point arithmetic?

---

## Comment 1

> Username: jzmaddock  
> Created at: 2020-05-22 08:29:52 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/238#issuecomment-632569221  

No, not yet.

---

## Comment 2

> Username: ckormanyos  
> Created at: 2020-05-22 12:01:55 UTC  
> Updated at: 2020-05-22 12:02:09 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/238#issuecomment-632656723  

>> Are there any backends that support fixed point arithmetic?  
  
> No, not yet.  
  
@johnmcfarlane has done some fascinating work in this area with [cnl](https://github.com/johnmcfarlane/cnl)  
  
We also made a serious effort during Boost GSoC 2015 [fixed-point](https://github.com/BoostGSoC15/fixed_point)  
  
In the GSoC '15 work, we had a difference of opinion with some other experts regarding certain technical issues. Other than that, the `negatable` class from the GSoC 2015 experience has some really cool fixed-point code. I still use it _on the metal_ in  embedded systems.  
  
It would really only take a good few weeks to adapt some of that code for a legit backend for multiprecision, and I think we should mark fixed-point backend as a potential enhancement (GSoC' 21 maybe).

---

## Comment 3

> Username: NAThompson  
> Created at: 2020-05-22 15:14:54 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/238#issuecomment-632746765  

Thanks guys; useful info.
