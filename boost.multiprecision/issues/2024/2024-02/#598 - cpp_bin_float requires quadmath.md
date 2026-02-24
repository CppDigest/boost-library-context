# #598 - cpp_bin_float requires quadmath? [Closed]

> Username: NAThompson  
> Created at: 2024-02-09 22:25:50 UTC  
> Updated at: 2024-02-10 12:34:22 UTC  
> Closed at: 2024-02-10 12:34:22 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/598  

In [this](https://github.com/boostorg/math/actions/runs/7849695801/job/21423588174?pr=1081) build, we observe the following error:  
  
```  
In file included from condition_number_test.cpp:10:  
../../../boost/multiprecision/cpp_bin_float.hpp: In instantiation of ‘typename std::enable_if<(std::is_same<Float, __float128>::value && (std::numeric_limits<Type>::digits <= Digits)), boost::multiprecision::backends::cpp_bin_float<Digits, DigitBase, Allocator, Exponent, MinExponent, MaxExponent>&>::type boost::multiprecision::backends::cpp_bin_float<Digits, DigitBase, Allocator, Exponent, MinExponent, MaxExponent>::assign_float(Float) [with Float = __float128; unsigned int Digits = 113; boost::multiprecision::backends::digit_base_type DigitBase = boost::multiprecision::backends::digit_base_10; Allocator = void; Exponent = int; Exponent MinExponent = 0; Exponent MaxExponent = 0; typename std::enable_if<(std::is_same<Float, __float128>::value && (std::numeric_limits<Type>::digits <= Digits)), boost::multiprecision::backends::cpp_bin_float<Digits, DigitBase, Allocator, Exponent, MinExponent, MaxExponent>&>::type = boost::multiprecision::backends::cpp_bin_float<113, boost::multiprecision::backends::digit_base_10, void, int, 0, 0>&]’:  
...  
/cygdrive/d/a/math/boost-root/libs/math/test/../../../boost/multiprecision/cpp_bin_float.hpp:316:(.text$_ZN5boost14multiprecision8backends13cpp_bin_floatILj113ELNS1_15digit_base_typeE10EviLi0ELi0EE12assign_floatIgEENSt9enable_ifIXaasrSt7is_sameIT_gE5valuelesrSt14numeric_limitsIS8_E6digitsLj113EERS4_E4typeES8_[_ZN5boost14multiprecision8backends13cpp_bin_floatILj113ELNS1_15digit_base_typeE10EviLi0ELi0EE12assign_floatIgEENSt9enable_ifIXaasrSt7is_sameIT_gE5valuelesrSt14numeric_limitsIS8_E6digitsLj113EERS4_E4typeES8_]+0x83): undefined reference to `signbitq'  
```  
  
My understanding was that cpp_bin_float did not require use of the quadmath library; is this understanding correct?

---

## Comment 1

> Username: jzmaddock  
> Created at: 2024-02-10 09:05:41 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/598#issuecomment-1936946215  

>My understanding was that cpp_bin_float did not require use of the quadmath library; is this understanding correct?  
  
It doesn't in itself, but conversion to and from a `__float128` does require libquadmath.  And there are some things in Math that will use __float128 quad precision literals when using cpp_bin_float_quad specifically.
