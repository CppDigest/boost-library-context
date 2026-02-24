# #837 - Compute average by keys [Open]

> Username: rgreenblatt  
> Created at: 2019-06-11 19:17:37 UTC  
> Updated at: 2019-06-11 19:17:37 UTC  
> Url: https://github.com/boostorg/compute/issues/837  

I need to compute the average of a vector of 3d points (stored as float4) grouped by another vector of keys. The easiest approach I can think of is to store a count in the 4th value of the float4 (which I am not using otherwise), initialize the count to 1, and then sum using reduce_by_keys. This seems kind of dirty, so I was wondering if there was a better way. Is there something like [numpy's bincount](https://docs.scipy.org/doc/numpy/reference/generated/numpy.bincount.html) that I haven't found?   
  
Thanks!
