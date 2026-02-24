# #79 - Mark small_vector move operations noexcept [Closed]

> Username: Lastique  
> Created at: 2018-08-15 10:10:15 UTC  
> Updated at: 2018-11-10 23:21:33 UTC  
> Closed at: 2018-11-10 23:21:33 UTC  
> Url: https://github.com/boostorg/container/issues/79  

It should be possible to mark `small_vector` move constructor and move assignment `noexcept` if the corresponding move operations of the `value_type` are `noexcept`. This would be useful in multiple contexts, like generating `noexcept` defaulted constructors and operators in classes that contain `small_vector` objects.

---

## Comment 1

> Username: igaztanaga  
> Created at: 2018-11-10 23:21:33 UTC  
> Url: https://github.com/boostorg/container/issues/79#issuecomment-437629692  

Many thanks for the report. The assignment operator needs to check also allocator-related conditions. Fixed in:  
  
https://github.com/boostorg/container/commit/059133a3453378147f2bee2d9968544f64213936
