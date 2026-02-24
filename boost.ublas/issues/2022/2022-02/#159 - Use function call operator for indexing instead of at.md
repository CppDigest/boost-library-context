# #159 - Use function call operator for indexing instead of at [Open]

> Username: bassoy  
> Created at: 2022-02-09 14:23:18 UTC  
> Updated at: 2022-02-10 15:32:54 UTC  
> Url: https://github.com/boostorg/ublas/issues/159  

### Discussed in https://github.com/boostorg/ublas/discussions/141  
  
<div type='discussions-op-text'>  
  
<sup>Originally posted by **bassoy** October 21, 2021</sup>  
Right now index accessing is performed using the `at` member function in tensor e.g. in [tensor_dynamic](https://github.com/boostorg/ublas/blob/develop/include/boost/numeric/ublas/tensor/tensor/tensor_dynamic.hpp), see also in [example](https://github.com/boostorg/ublas/blob/develop/examples/tensor/access_tensor.cpp).  
  
```cpp  
for(auto i = 0u; i < B.size(0); ++i)  
  for(auto j = 0u; j < B.size(1); ++j)  
        C.at(i,j) = std::conj(B.at(i,j));  
```  
  
Is it possible to overload `operator()` for this purpose? Note that `operator()` will also be used for creating subtensors.  
  
```cpp  
for(auto i = 0u; i < B.size(0); ++i)  
  for(auto j = 0u; j < B.size(1); ++j)  
        C(i,j) = std::conj(B(i,j));  
```  
We could also use `operator[]` for accessing single indices and multi-indices.  
```cpp  
for(auto i = 0u; i < B.size(0); ++i)  
  for(auto j = 0u; j < B.size(1); ++j)  
        C[i,j] = std::conj(B[i,j]);  
```  
  
This will be again closer to a Matlab or Octave or R notation.   
</div>

---

## Comment 1

> Username: bassoy  
> Created at: 2022-02-09 14:24:03 UTC  
> Url: https://github.com/boostorg/ublas/issues/159#issuecomment-1033814969  

The implementation should follow https://github.com/boostorg/ublas/discussions/141#discussioncomment-2141497

---

## Comment 2

> Username: adrishyantee  
> Created at: 2022-02-10 15:32:53 UTC  
> Url: https://github.com/boostorg/ublas/issues/159#issuecomment-1035060026  

I want to work for the proposal [here](https://github.com/boostorg/ublas/discussions/141#discussioncomment-2141497). Can you guide me through this?
