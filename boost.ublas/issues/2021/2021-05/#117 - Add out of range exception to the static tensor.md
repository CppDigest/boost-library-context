# #117 - Add out of range exception to the static tensor [Open]

> Username: amitsingh19975  
> Created at: 2021-05-26 12:25:27 UTC  
> Updated at: 2021-05-26 12:25:27 UTC  
> Url: https://github.com/boostorg/ublas/issues/117  

The static tensor does not check for out of range error, which makes the read and writes of invalid memory or segmentation fault easier.  
  
```cpp  
auto t = tensor_static<float,extents<1,2,3>>{4.f};  
t.at(1,2,3) = 10.f; // no exception triggered.  
```
