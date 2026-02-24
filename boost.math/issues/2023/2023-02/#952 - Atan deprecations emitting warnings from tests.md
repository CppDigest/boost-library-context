# #952 - Atan deprecations emitting warnings from tests [Closed]

> Username: NAThompson  
> Created at: 2023-02-15 04:21:09 UTC  
> Updated at: 2023-02-15 04:24:22 UTC  
> Closed at: 2023-02-15 04:24:21 UTC  
> Url: https://github.com/boostorg/math/issues/952  

```  
math/build$ cmake ../ -G Ninja  
math/build$ ninja  
~/math/include/boost/math/complex/atanh.hpp:24:3: note: 'atanh<double>' has been explicitly marked deprecated here  
[[deprecated("Replaced by C++11")]] std::complex<T> atanh(const std::complex<T>& z)  
  ^  
~/math/test/compile_test/compl_atanh_incl_test.cpp:24:58: warning: 'atanh<long double>' is deprecated: Replaced by C++11 [-Wdeprecated-declarations]  
   check_result<std::complex<long double> >(boost::math::atanh(std::complex<long double>()));  
                                                         ^  
~/math/include/boost/math/complex/atanh.hpp:24:3: note: 'atanh<long double>' has been explicitly marked deprecated here  
[[deprecated("Replaced by C++11")]] std::complex<T> atanh(const std::complex<T>& z)  
  ^  
3 warnings generated.  
```

---

## Comment 1

> Username: mborland  
> Created at: 2023-02-15 04:22:52 UTC  
> Url: https://github.com/boostorg/math/issues/952#issuecomment-1430738304  

https://github.com/boostorg/math/pull/917

---

## Comment 2

> Username: NAThompson  
> Created at: 2023-02-15 04:24:21 UTC  
> Url: https://github.com/boostorg/math/issues/952#issuecomment-1430739000  

My bad!
