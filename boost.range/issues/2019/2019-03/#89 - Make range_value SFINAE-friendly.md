# #89 - Make range_value SFINAE-friendly [Open]

> Username: Lastique  
> Created at: 2019-03-22 18:15:13 UTC  
> Updated at: 2019-03-22 18:15:13 UTC  
> Url: https://github.com/boostorg/range/issues/89  

Currently (Boost 1.69) `range_value<T>::type` hard-fails to compile if `T` is not a range type. This is because it unconditionally uses `range_iterator<T>::type` and that type is missing in that case. Please, make `range_value` also SFINAE-friendly, i.e. so that it doesn't hard-fail the compilation but rather doesn't define the `type` type if `T` is not a range type.
