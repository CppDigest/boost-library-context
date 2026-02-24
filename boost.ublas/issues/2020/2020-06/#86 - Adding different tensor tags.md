# #86 - Adding different tensor tags [Closed]

> Username: amitsingh19975  
> Created at: 2020-06-06 13:40:44 UTC  
> Updated at: 2020-06-06 15:17:35 UTC  
> Closed at: 2020-06-06 15:17:35 UTC  
> Url: https://github.com/boostorg/ublas/issues/86  

### Tags  
* dynamic_shape  
* dynamic_layout  
* dynamic_storage  
* static_shape<Extents...>  
* static_layout<Layouts...>  
* static_storage  
* fixed_shape<Size>  
* fixed_layout<Size>  
  
These tags will be used for custom tensor types  
```cpp  
using my_tensor = tensor< tag::dynamic_shape, tag::static_layout<1,2,3>, tag::dynamic_storage>;  
```
