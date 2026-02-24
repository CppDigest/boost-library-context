# #884 Check for nullptr when getting appdata_path [Open]

> Username: dmedora  
> Created at: 2024-04-23 21:03:29 UTC  
> Updated at: 2024-04-24 14:07:51 UTC  
> Url: https://github.com/boostorg/compute/pull/884  

At present, there is no check to ensure `std::getenv` has returned a non-null value in [detail::getenv]()https://github.com/boostorg/compute/blob/36350b7de849300bd3d72a05d8bf890ca405a014/include/boost/compute/detail/getenv.hpp#L26.   
  
If it is unable to find the APPDATA or HOME env var, it returns a nullptr. `appdata_path` then tries to use this nullptr [to build a string](https://github.com/boostorg/compute/blob/36350b7de849300bd3d72a05d8bf890ca405a014/include/boost/compute/detail/path.hpp#L37). Updated appdata_path to log an error if a nullptr is returned.   
  
(Context: ran into an issue with this while using [LightGBM](https://github.com/microsoft/LightGBM/) where the program was unexpectedly crashing without any error due to $HOME not being set.)

---
