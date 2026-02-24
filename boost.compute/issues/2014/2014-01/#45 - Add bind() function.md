# #45 - Add bind() function [Closed]

> Username: kylelutz  
> Created at: 2014-01-07 03:40:42 UTC  
> Updated at: 2014-07-30 01:51:21 UTC  
> Closed at: 2014-07-30 01:51:21 UTC  
> Url: https://github.com/boostorg/compute/issues/45  

Add support for a `bind()` function which can compose functions like `std`/`boost::bind()`.  
  
For example, the user should be able to write code like the following to compute the cube of each value:  
  
```  
transform(vec.begin(), vec.end(), vec.begin(), bind(pow<float>(), _1, 3.0f)), queue);`  
```

---

## Comment 1

> Username: kylelutz  
> Created at: 2014-07-27 17:35:17 UTC  
> Url: https://github.com/boostorg/compute/issues/45#issuecomment-50279963  

Implemented in PR #200.
