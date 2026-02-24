# #810 - Set operation strategies are undocumented [Open]

> Username: vissarion  
> Created at: 2021-02-15 15:38:46 UTC  
> Updated at: 2021-02-22 11:15:27 UTC  
> Url: https://github.com/boostorg/geometry/issues/810  

In documentation, there are links for set operations with the use of strategies e.g. difference (with strategy), see below for more. However, the description on the use of the strategy seems loose, e.g. the example is identical to the original (without strategy) function.   
  
Is this on purpose? If not this could be fixed.   
  
https://www.boost.org/doc/libs/1_75_0/libs/geometry/doc/html/geometry/reference/algorithms/difference/difference_4_with_strategy.html  
https://www.boost.org/doc/libs/1_75_0/libs/geometry/doc/html/geometry/reference/algorithms/union_/union__4_with_strategy.html  
https://www.boost.org/doc/libs/1_75_0/libs/geometry/doc/html/geometry/reference/algorithms/intersection/intersection_4_with_strategy.html  
https://www.boost.org/doc/libs/1_75_0/libs/geometry/doc/html/geometry/reference/algorithms/sym_difference/sym_difference_4_with_strategy.html

---

## Comment 1

> Username: pcmourya  
> Created at: 2021-02-16 11:01:09 UTC  
> Url: https://github.com/boostorg/geometry/issues/810#issuecomment-779761617  

I want to work on this, may I proceed with that?

---

## Comment 2

> Username: mloskot  
> Created at: 2021-02-16 11:03:32 UTC  
> Url: https://github.com/boostorg/geometry/issues/810#issuecomment-779762932  

@pcmourya No need to ask for permission. There is no reservation of issues or tasks in use. First PR submitted, first PR reviewed and first PR has chance to be merged first.

---

## Comment 3

> Username: ayushgupta138  
> Created at: 2021-02-21 15:20:23 UTC  
> Updated at: 2021-02-21 16:14:02 UTC  
> Url: https://github.com/boostorg/geometry/issues/810#issuecomment-782874508  

I could not find any pre defined strategies in the official documentation for set operations mentioned above. So do we have to define a custom strategy and add it to the example codes?  
It would be helpful if @vissarion could clarify on this.

---

## Comment 4

> Username: Siddharth-coder13  
> Created at: 2021-02-22 09:16:40 UTC  
> Url: https://github.com/boostorg/geometry/issues/810#issuecomment-783224041  

> is this on purpose? If not this could be fixed.  
  
I think strategies for these operations are not yet defined, that's why the same example is used on both functions. But using the same example on both functions can be misleading, I guess it needs to be removed from the `with strategies` part, till the strategies are defined.

---

## Comment 5

> Username: ayushgupta138  
> Created at: 2021-02-22 10:49:05 UTC  
> Url: https://github.com/boostorg/geometry/issues/810#issuecomment-783283114  

I agree with @Siddharth-coder13.

---

## Comment 6

> Username: vissarion  
> Created at: 2021-02-22 11:15:27 UTC  
> Url: https://github.com/boostorg/geometry/issues/810#issuecomment-783299574  

I think it is OK to stay as is until the strategies are documented and properly linked as available strategies in the algorithm documentation. Also note that recently set operations were refactored see https://github.com/boostorg/geometry/pull/768
