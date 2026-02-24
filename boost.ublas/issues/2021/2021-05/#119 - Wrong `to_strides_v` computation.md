# #119 - Wrong `to_strides_v` computation [Open]

> Username: amitsingh19975  
> Created at: 2021-05-26 16:16:10 UTC  
> Updated at: 2021-06-04 19:04:25 UTC  
> Url: https://github.com/boostorg/ublas/issues/119  

The strides giving wrong strides for static extents, which is not consistent with the other extents types.  
  
```cpp  
    namespace ublas = boost::numeric::ublas;  
    using e_t = ublas::extents<2,2>;  
    auto s = to_strides_v<e_t, ublas::layout::first_order>; // [1,2], but the right strides should be [1,1]  
```
