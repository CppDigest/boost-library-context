# #33 - Implement set algorithms [Closed]

> Username: kylelutz  
> Created at: 2013-12-21 19:37:19 UTC  
> Updated at: 2014-05-18 22:24:09 UTC  
> Closed at: 2014-05-18 22:24:09 UTC  
> Url: https://github.com/boostorg/compute/issues/33  

Implement the various set algorithms from the STL. These algorithms take advantage of sorted inputs and can be accellerated on the GPU.  
  
The algorithms are:  
- `set_difference()`  
- `set_intersection()`  
- `set_union()`  
- `set_symmetric_difference()`  
- `includes()`

---

## Comment 1

> Username: kylelutz  
> Created at: 2014-05-18 22:24:09 UTC  
> Url: https://github.com/boostorg/compute/issues/33#issuecomment-43455500  

Implemented in PR #113 and PR #115.
