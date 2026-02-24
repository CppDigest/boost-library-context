# #242 - Sum of weighted_mean is not weighted_mean() [Closed]

> Username: henryiii  
> Created at: 2019-10-23 20:35:32 UTC  
> Updated at: 2019-10-26 09:59:58 UTC  
> Closed at: 2019-10-26 09:59:58 UTC  
> Url: https://github.com/boostorg/histogram/issues/242  

The sum of a `weighted_mean` does not match a default constructed `weighted_mean`.  
  
```python  
>>> h = bh.histogram((10,0,1), storage=bh.storage.weighted_mean)  
>>> bh.accumulators.weighted_mean()  
weighted_mean(wsum=0 wsum2=..., value=0, variance=nan)  
>>> h.sum()  
weighted_mean(wsum=0 wsum2=..., value=nan, variance=nan)  
```  
  
(Pardon my Python)
