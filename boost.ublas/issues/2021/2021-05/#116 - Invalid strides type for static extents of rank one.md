# #116 - Invalid strides type for static extents of rank one [Open]

> Username: amitsingh19975  
> Created at: 2021-05-25 17:35:32 UTC  
> Updated at: 2021-05-25 17:35:32 UTC  
> Url: https://github.com/boostorg/ublas/issues/116  

Right now, the static tensor gets confused about which strides type it should use for rank one extents because of the ambiguity between the `extents_static_rank<4>` and `extents_static<4>`. This ambiguity occurs because, behind the scene, they both are the specialization of the `extents_core<...>`.  
  
When we create a static tensor of rank one, instead of choosing the strides from the `extents_static`, it chooses the strides from the `extents_static_rank`.  
  
```cpp  
auto t = tensor_static<float, extents<2>>(); // error: no viable conversion from 'const array<[...], 1>' to 'const array<[...], 2UL aka 2>'  
```
