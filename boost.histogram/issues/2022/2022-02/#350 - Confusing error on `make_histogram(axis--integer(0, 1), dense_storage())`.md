# #350 - Confusing error on `make_histogram(axis::integer(0, 1), dense_storage())` [Open]

> Username: HDembinski  
> Created at: 2022-02-04 15:25:14 UTC  
> Updated at: 2022-02-04 15:26:26 UTC  
> Url: https://github.com/boostorg/histogram/issues/350  

```c++  
// c++17  
auto h = bh::make_histogram(bh::axis::integer(0, 1), bh::dense_storage());  
```  
This does not work (and currently shouldn't!) but fails in mysterious ways that are difficult to comprehend. If possible, we should make this work and deprecate `make_histogram_with` or fail at the earliest. The code interprets the second argument as an axis, but hopefully we can prevent that with better constraints on the template.
