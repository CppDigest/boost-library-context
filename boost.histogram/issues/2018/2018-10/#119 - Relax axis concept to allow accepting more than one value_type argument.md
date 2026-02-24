# #119 - Relax axis concept to allow accepting more than one value_type argument [Closed]

> Username: HDembinski  
> Created at: 2018-10-10 12:29:27 UTC  
> Updated at: 2018-11-04 21:56:50 UTC  
> Closed at: 2018-11-02 09:41:21 UTC  
> Url: https://github.com/boostorg/histogram/issues/119  

This is needed to make an axis with hexagonal bins or any other non-orthogonal binning.

---

## Comment 1

> Username: HDembinski  
> Created at: 2018-11-02 09:41:21 UTC  
> Url: https://github.com/boostorg/histogram/issues/119#issuecomment-435323857  

This turns out to be very difficult in this library, where the actual axis is sometimes hidden behind a generic variant. The caller of a variant has to know at compile-time, whether the axis accepts one or two or more arguments.  
  
So this feature will not be supported, an axis with hexagonal bins can be implemented nevertheless by passing pairs of values as tuples or some vector type.

---

## Comment 2

> Username: HDembinski  
> Created at: 2018-11-04 21:56:50 UTC  
> Url: https://github.com/boostorg/histogram/issues/119#issuecomment-435711665  

Actually, now implemented in the develop branch. The axis needs to accept a std::tuple for this to work. If the histogram has only one axis, and if that axis accepts multiple parameters, this code also works  
```  
auto h = make_histogram(my_3d_axis());  
h(1, 2, 3); // args are correctly forwarded as tuple to axis  
```
