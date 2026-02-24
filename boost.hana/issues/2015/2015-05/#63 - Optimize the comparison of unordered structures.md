# #63 - [Map, Set] Optimize the comparison of unordered structures [Closed]

> Username: ldionne  
> Created at: 2015-05-04 16:41:03 UTC  
> Updated at: 2015-08-23 18:41:12 UTC  
> Closed at: 2015-08-23 18:41:12 UTC  
> Url: https://github.com/boostorg/hana/issues/63  

In a `Map` and a `Set`, since the order of elements in unspecified, we can in theory compare the objects in the order we want. Hence, we could probably write something like   
  
``` cpp  
 {xs...} == {ys...}  <=>  all_of([(xs == ys)...])  
```

---

## Comment 1

> Username: ldionne  
> Created at: 2015-08-23 15:33:25 UTC  
> Url: https://github.com/boostorg/hana/issues/63#issuecomment-133867783  

The proposed implementation above won't work, since `xs` and `ys` could be in a different order. However, we can write   
  
``` c++  
    {xs...} == {ys...} <=> sizeof...(xs) == sizeof...(ys) && all_of([xs in {ys...}])  
```  
  
which is probably faster than a non-eager version, which requires recursion.
