# #64 - Bug: Typo in return value of adaptor::operator() [Closed]

> Username: patrickroberts  
> Created at: 2023-11-22 18:34:33 UTC  
> Updated at: 2024-03-04 04:38:08 UTC  
> Closed at: 2024-03-04 04:38:08 UTC  
> Url: https://github.com/boostorg/stl_interfaces/issues/64  

There's a typo in this class:  
  
https://github.com/boostorg/stl_interfaces/blob/510eb431fbf64e98b6870468aa523dd4c6a6c69a/include/boost/stl_interfaces/view_adaptor.hpp#L286-L313  
  
on this line:  
  
https://github.com/boostorg/stl_interfaces/blob/510eb431fbf64e98b6870468aa523dd4c6a6c69a/include/boost/stl_interfaces/view_adaptor.hpp#L298  
  
`f` should be `f_` to refer to the data member, but instead when the constexpr if statement is true and the expression isn't discarded, it is a compilation error. Effectively when `BOOST_STL_INTERFACES_USE_CONCEPTS` is set, `adaptor` can only be used with the pipe syntax (when the constexpr if is false) and cannot be invoked as the original function.  
  
The reason it is only a compilation error when the statement isn't discarded is because the identifier `f` would only need to be resolved for the instantiation of `operator()` method template, since it could be ADL.

---

## Comment 1

> Username: tzlaine  
> Created at: 2024-03-04 04:38:08 UTC  
> Url: https://github.com/boostorg/stl_interfaces/issues/64#issuecomment-1975685213  

Thanks!  This was fixed in 0f422d58cb39, and will appear in the next Boost release.
