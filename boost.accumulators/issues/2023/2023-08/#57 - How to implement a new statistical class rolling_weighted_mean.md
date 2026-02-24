# #57 - How to implement a new statistical class rolling_weighted_mean? [Open]

> Username: JaydenFish  
> Created at: 2023-08-31 15:26:08 UTC  
> Updated at: 2023-09-03 14:12:45 UTC  
> Url: https://github.com/boostorg/accumulators/issues/57  

It is complexed to mix rolling and weighted.  
thanks very much.

---

## Comment 1

> Username: yuvalif  
> Created at: 2023-09-03 14:12:44 UTC  
> Url: https://github.com/boostorg/accumulators/issues/57#issuecomment-1704317195  

it depends what kind of functionality you want.   
you can just add a weight that will be stored together with the value and treated similarly to how "weighted_mean" is using the weight (just multiply with the value).  
another option is to have fixed weights associated with the positions in the window (= a filter), here the implementation is different, but should not be too complex.
