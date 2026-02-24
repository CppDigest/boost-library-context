# #121 - Exception Raised for Static Rank Tensor inside the Tensor Times Tensor Product [Open]

> Username: amitsingh19975  
> Created at: 2021-05-30 13:41:38 UTC  
> Updated at: 2021-05-30 13:41:38 UTC  
> Url: https://github.com/boostorg/ublas/issues/121  

This exception is raised because the static rank extents for result type are not filled with one but instead default initialized to zero, making the extents invalid.  
  
```cpp   
auto A = tensor_static_rank<float,2>(4,3);  
auto B = tensor_static_rank<float,3>(3,4,2);  
auto AB = A(_d,_f) * B(_f,_d,_); // Throws the exception  
```
