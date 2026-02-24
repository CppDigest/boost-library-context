# #1048 - ccmath::floor/ceil fail for large arguments [Closed]

> Username: jzmaddock  
> Created at: 2023-11-14 16:09:48 UTC  
> Updated at: 2023-11-17 09:59:12 UTC  
> Closed at: 2023-11-17 09:59:12 UTC  
> Url: https://github.com/boostorg/math/issues/1048  

When calling floor/ceil with `std::numeric_limits<T>::max()` or indeed any value larger than max/2, then this line will overflow:  
  
https://github.com/boostorg/math/blob/8c72a85ec6c6e08ab58a18053db6275bd15f939a/include/boost/math/ccmath/floor.hpp#L32  
  
Which then means this will loop forever:  
  
https://github.com/boostorg/math/blob/8c72a85ec6c6e08ab58a18053db6275bd15f939a/include/boost/math/ccmath/floor.hpp#L34  
  
Which results in:  
  
```  
1>D:\data\boost\boost\boost\math\ccmath\floor.hpp(32,20): warning C4056: overflow in floating-point constant arithmetic  
1>D:\data\boost\boost\boost\math\ccmath\floor.hpp(76,30): message : while evaluating constexpr function 'boost::math::ccmath::detail::floor_pos_impl'  
1>D:\data\boost\boost\boost\math\ccmath\floor.hpp(94,55): message : while evaluating constexpr function 'boost::math::ccmath::detail::floor_impl'  
1>D:\data\boost\boost\boost\math\special_functions\trunc.hpp(38,73): message : while evaluating constexpr function 'boost::math::ccmath::floor'  
1>D:\data\boost\boost\boost\math\special_functions\trunc.hpp(52,24): message : while evaluating constexpr function 'boost::math::detail::trunc'  
1>D:\data\boost\boost\boost\math\special_functions\trunc.hpp(57,16): message : while evaluating constexpr function 'boost::math::trunc'  
1>D:\data\boost\boost\libs\math\test\constexpr_trunc_test.cpp(42,94): message : while evaluating constexpr function 'boost::math::trunc'  
1>D:\data\boost\boost\libs\math\test\constexpr_trunc_test.cpp(72,5): message : see reference to function template instantiation 'void test<float>(void)' being compiled  
1>D:\data\boost\boost\libs\math\test\constexpr_trunc_test.cpp(42,94): error C2131: expression did not evaluate to a constant  
1>D:\data\boost\boost\boost\math\ccmath\floor.hpp(36,13): message : failure was caused by evaluation exceeding step limit of 10000000 (/constexpr:steps<NUMBER>)  
1>D:\data\boost\boost\libs\math\test\constexpr_trunc_test.cpp(42,45): error C2131: expression did not evaluate to a constant  
1>D:\data\boost\boost\boost\math\ccmath\floor.hpp(36,13): message : failure was caused by evaluation exceeding step limit of 10000000 (/constexpr:steps<NUMBER>)  
```  
  
I *think* one option would be to spot that for any value greater than 1/epsilon then floor/ceil are the identity function?  
  
I also note that these functions are inefficient for large arguments, so for max/3 as the argument I get:  
  
```  
1>D:\data\boost\boost\IDE\vs2022\math\scrap\scrap.cpp(9,23): error C2131: expression did not evaluate to a constant  
1>d:\data\boost\boost\boost\math\ccmath\floor.hpp(36,13): message : failure was caused by evaluation exceeding step limit of 1048576 (/constexpr:steps<NUMBER>)  
```  
  
Which is caused by the extreme number of steps required to reach the result, even though there is no longer any overflow.
