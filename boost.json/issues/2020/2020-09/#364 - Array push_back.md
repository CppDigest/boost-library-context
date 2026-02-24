# #364 - Array push_back [Closed]

> Username: djarek  
> Created at: 2020-09-18 23:07:46 UTC  
> Updated at: 2020-09-20 20:19:12 UTC  
> Closed at: 2020-09-20 20:19:12 UTC  
> Url: https://github.com/boostorg/json/issues/364  

Currently, calling `json_array.push_back(json_array.back())` results in undefined behavior if `push_back` causes a reallocation. This is different behavior than exhibited by `std::vector`, for which self-referential `push_back` is valid. Either the documentation should contain an explicit warning about this difference, or the implementation should be amended to follow `std::vector`'s practice.  
  
Additionally, the documentation for `push_back` claims the Strong Exception guarantee, however, the implementation invalidates references if constructing the array element throws.  
  
Note that both issues apply to `emplace_back` as well.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-09-20 20:19:12 UTC  
> Url: https://github.com/boostorg/json/issues/364#issuecomment-695831349  

Should work now thanks
