# #118 - Exception raised on the construction of the tensor from the `tensor_expression` [Open]

> Username: amitsingh19975  
> Created at: 2021-05-26 14:07:26 UTC  
> Updated at: 2022-02-09 15:49:20 UTC  
> Url: https://github.com/boostorg/ublas/issues/118  

When constructing expression such as `A + 3 * B` and constructing a tensor out of the expression, the exception is raised, which should not happen.  
  
```cpp  
    using tensor_t = tensor_dynamic<float>;  
    auto const e = extents<>{3,2};  
  
    auto const a = tensor_t(e, 1.f);  
  
    auto const three = 3.f;  
    auto const expr = a + three * a;  
    auto const t = tensor_t(expr); // exception  
```
