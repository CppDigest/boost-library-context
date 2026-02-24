# #272 - Compilation success depends on typedef [Closed]

> Username: NAThompson  
> Created at: 2020-10-14 17:22:37 UTC  
> Updated at: 2020-10-14 17:52:12 UTC  
> Closed at: 2020-10-14 17:52:12 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/272  

Using `g++-10` with no standard flags:  
  
  
```cpp  
#include <boost/multiprecision/cpp_bin_float.hpp>  
//This compiles without issue:  
//using Real = boost::multiprecision::cpp_bin_float_100;  
// This one fails:  
using Real = boost::multiprecision::cpp_bin_float<500>;  
  
int main() {  
    size_t n = 10;  
    Real d{n};  
}  
```  
  
This fails with:  
  
```  
g++-10 -I../boost reproduce.cpp  
reproduce.cpp: In function 'int main()':  
reproduce.cpp:7:13: error: no matching function for call to 'boost::multiprecision::backends::cpp_bin_float<500>::cpp_bin_float(<brace-enclosed initializer list>)'  
    7 |     Real d{n};  
      |             ^  
In file included from reproduce.cpp:1:  
../boost/boost/multiprecision/cpp_bin_float.hpp:160:13: note: candidate: 'template<class Float> boost::multiprecision::backends::cpp_bin_float<Digits, DigitBase, Allocator, Exponent, MinExponent, MaxExponent>::cpp_bin_float(const Float&, const typename boost::enable_if_c<(boost::is_same<V, __float128>::value && ((int)(boost::multiprecision::backends::cpp_bin_float<Digits, DigitBase, Allocator, Exponent, MinExponent, MaxExponent>::bit_count) < 113))>::type*) [with Float = Float; unsigned int Digits = 500; boost::multiprecision::backends::digit_base_type DigitBase = boost::multiprecision::backends::digit_base_10; Allocator = void; Exponent = int; Exponent MinExponent = 0; Exponent MaxExponent = 0]'  
  160 |    explicit cpp_bin_float(const Float& f,  
      |             ^~~~~~~~~~~~~  
../boost/boost/multiprecision/cpp_bin_float.hpp:160:13: note:   template argument deduction/substitution failed:  
../boost/boost/multiprecision/cpp_bin_float.hpp: In substitution of 'template<class Float> boost::multiprecision::backends::cpp_bin_float<500>::cpp_bin_float(const Float&, const typename boost::enable_if_c<(boost::is_same<V, __float128>::value && ((int)(boost::multiprecision::backends::cpp_bin_float<500>::bit_count) < 113)), void>::type*) [with Float = long unsigned int]':  
reproduce.cpp:7:13:   required from here  
../boost/boost/multiprecision/cpp_bin_float.hpp:160:13: error: no type named 'type' in 'struct boost::enable_if_c<false, void>'  
../boost/boost/multiprecision/cpp_bin_float.hpp:152:4: note: candidate: 'template<class Float> boost::multiprecision::backends::cpp_bin_float<Digits, DigitBase, Allocator, Exponent, MinExponent, MaxExponent>::cpp_bin_float(const Float&, const typename boost::enable_if_c<(boost::is_same<V, __float128>::value && ((int)(boost::multiprecision::backends::cpp_bin_float<Digits, DigitBase, Allocator, Exponent, MinExponent, MaxExponent>::bit_count) >= 113))>::type*) [with Float = Float; unsigned int Digits = 500; boost::multiprecision::backends::digit_base_type DigitBase = boost::multiprecision::backends::digit_base_10; Allocator = void; Exponent = int; Exponent MinExponent = 0; Exponent MaxExponent = 0]'  
  152 |    cpp_bin_float(const Float& f,  
      |    ^~~~~~~~~~~~~  
../boost/boost/multiprecision/cpp_bin_float.hpp:152:4: note:   template argument deduction/substitution failed:  
../boost/boost/multiprecision/cpp_bin_float.hpp: In substitution of 'template<class Float> boost::multiprecision::backends::cpp_bin_float<500>::cpp_bin_float(const Float&, const typename boost::enable_if_c<(boost::is_same<V, __float128>::value && ((int)(boost::multiprecision::backends::cpp_bin_float<500>::bit_count) >= 113)), void>::type*) [with Float = long unsigned int]':  
reproduce.cpp:7:13:   required from here  
../boost/boost/multiprecision/cpp_bin_float.hpp:152:4: error: no type named 'type' in struct boost::enable_if_c<false, void>'  
../boost/boost/multiprecision/cpp_bin_float.hpp:144:13: note: candidate: 'template<class Float> boost::multiprecision::backends::cpp_bin_float<Digits, DigitBase, Allocator, Exponent, MinExponent, MaxExponent>::cpp_bin_float(const Float&, const typename boost::enable_if_c<boost::multiprecision::backends::detail::is_cpp_bin_float_explicitly_constructible_from_type<Float, boost::multiprecision::backends::cpp_bin_float<Digits, DigitBase, Allocator, Exponent, MinExponent, MaxExponent>::bit_count>::value>::type*) [with Float = Float; unsigned int Digits = 500; boost::multiprecision::backends::digit_base_type DigitBase = boost::multiprecision::backends::digit_base_10; Allocator = void; Exponent = int; Exponent MinExponent = 0; Exponent MaxExponent = 0]'  
  144 |    explicit cpp_bin_float(const Float& f,  
      |             ^~~~~~~~~~~~~  
../boost/boost/multiprecision/cpp_bin_float.hpp:144:13: note:   template argument deduction/substitution failed:  
../boost/boost/multiprecision/cpp_bin_float.hpp: In substitution of 'template<class Float> boost::multiprecision::backends::cpp_bin_float<500>::cpp_bin_float(const Float&, const typename boost::enable_if_c<boost::multiprecision::backends::detail::is_cpp_bin_float_explicitly_constructible_from_type<Float, 1663, (boost::multiprecision::number_category<Backend>::value == boost::multiprecision::number_kind_floating_point)>::value, void>::type*) [with Float = long unsigned int]':  
reproduce.cpp:7:13:   required from here  
../boost/boost/multiprecision/cpp_bin_float.hpp:144:13: error: no type named 'type' in 'struct boost::enable_if_c<false, void>'  
../boost/boost/multiprecision/cpp_bin_float.hpp:136:4: note: candidate: 'template<class Float> boost::multiprecision::backends::cpp_bin_float<Digits, DigitBase, Allocator, Exponent, MinExponent, MaxExponent>::cpp_bin_float(const Float&, const typename boost::enable_if_c<boost::multiprecision::backends::detail::is_cpp_bin_float_implicitly_constructible_from_type<Float, boost::multiprecision::backends::cpp_bin_float<Digits, DigitBase, Allocator, Exponent, MinExponent, MaxExponent>::bit_count>::value>::type*) [with Float = Float; unsigned int Digits = 500; boost::multiprecision::backends::digit_base_type DigitBase = boost::multiprecision::backends::digit_base_10; Allocator = void; Exponent = int; Exponent MinExponent = 0; Exponent MaxExponent = 0]'  
  136 |    cpp_bin_float(const Float& f,  
      |    ^~~~~~~~~~~~~  
../boost/boost/multiprecision/cpp_bin_float.hpp:136:4: note:   template argument deduction/substitution failed:  
../boost/boost/multiprecision/cpp_bin_float.hpp: In substitution of 'template<class Float> boost::multiprecision::backends::cpp_bin_float<500>::cpp_bin_float(const Float&, const typename boost::enable_if_c<boost::multiprecision::backends::detail::is_cpp_bin_float_implicitly_constructible_from_type<Float, 1663, (boost::multiprecision::number_category<Backend>::value == boost::multiprecision::number_kind_floating_point)>::value, void>::type*) [with Float = long unsigned int]':  
reproduce.cpp:7:13:   required from here  
../boost/boost/multiprecision/cpp_bin_float.hpp:136:4: error: no type named 'type' in struct boost::enable_if_c<false, void>'  
../boost/boost/multiprecision/cpp_bin_float.hpp:130:13: note: candidate: 'template<unsigned int D, boost::multiprecision::backends::digit_base_type B, class A, class E, E MinE, E MaxE> boost::multiprecision::backends::cpp_bin_float<Digits, DigitBase, Allocator, Exponent, MinExponent, MaxExponent>::cpp_bin_float(const boost::multiprecision::backends::cpp_bin_float<D, B, A, E, MinE, MaxE>&, const typename boost::disable_if_c<(boost::multiprecision::backends::cpp_bin_float<Digits, DigitBase, Allocator, Exponent, MinExponent, MaxExponent>::bit_count >= boost::multiprecision::backends::cpp_bin_float<D, B, A, E, MinE, MaxE>::bit_count)>::type*) [with unsigned int D = D; boost::multiprecision::backends::digit_base_type B = B; A = A; E = E; E MinE = MinE; E MaxE = MaxE; unsigned int Digits = 500; boost::multiprecision::backends::digit_base_type DigitBase = boost::multiprecision::backends::digit_base_10; Allocator = void; Exponent = int; Exponent MinExponent = 0; Exponent MaxExponent = 0]'  
  130 |    explicit cpp_bin_float(const cpp_bin_float<D, B, A, E, MinE, MaxE>& o, typename boost::disable_if_c<(bit_count >= cpp_bin_float<D, B, A, E, MinE, MaxE>::bit_count)>::type const* = 0)  
      |             ^~~~~~~~~~~~~  
../boost/boost/multiprecision/cpp_bin_float.hpp:130:13: note:   template argument deduction/substitution failed:  
reproduce.cpp:7:13: note:   mismatched types 'const boost::multiprecision::backends::cpp_bin_float<Digits, DigitBase, Allocator, Exponent, MinE, MaxE>' and 'size_t' {aka 'long unsigned int'}  
    7 |     Real d{n};  
      |             ^  
In file included from reproduce.cpp:1:  
../boost/boost/multiprecision/cpp_bin_float.hpp:125:4: note: candidate: 'template<unsigned int D, boost::multiprecision::backends::digit_base_type B, class A, class E, E MinE, E MaxE> boost::multiprecision::backends::cpp_bin_float<Digits, DigitBase, Allocator, Exponent, MinExponent, MaxExponent>::cpp_bin_float(const boost::multiprecision::backends::cpp_bin_float<D, B, A, E, MinE, MaxE>&, const typename boost::enable_if_c<(boost::multiprecision::backends::cpp_bin_float<Digits, DigitBase, Allocator, Exponent, MinExponent, MaxExponent>::bit_count >= boost::multiprecision::backends::cpp_bin_float<D, B, A, E, MinE, MaxE>::bit_count)>::type*) [with unsigned int D = D; boost::multiprecision::backends::digit_base_type B = B; A = A; E = E; E MinE = MinE; E MaxE = MaxE; unsigned int Digits = 500; boost::multiprecision::backends::digit_base_type DigitBase = boost::multiprecision::backends::digit_base_10; Allocator = void; Exponent = int; Exponent MinExponent = 0; Exponent MaxExponent = 0]'  
  125 |    cpp_bin_float(const cpp_bin_float<D, B, A, E, MinE, MaxE>& o, typename boost::enable_if_c<(bit_count >= cpp_bin_float<D, B, A, E, MinE, MaxE>::bit_count)>::type const* = 0)  
      |    ^~~~~~~~~~~~~  
../boost/boost/multiprecision/cpp_bin_float.hpp:125:4: note:   template argument deduction/substitution failed:  
reproduce.cpp:7:13: note:   mismatched types 'const boost::multiprecision::backends::cpp_bin_float<Digits, DigitBase, Allocator, Exponent, MinE, MaxE>' and 'size_t' {aka 'long unsigned int'}  
    7 |     Real d{n};  
      |             ^  
In file included from reproduce.cpp:1:  
../boost/boost/multiprecision/cpp_bin_float.hpp:121:4: note: candidate: 'boost::multiprecision::backends::cpp_bin_float<Digits, DigitBase, Allocator, Exponent, MinExponent, MaxExponent>::cpp_bin_float(const boost::multiprecision::backends::cpp_bin_float<Digits, DigitBase, Allocator, Exponent, MinExponent, MaxExponent>&) [with unsigned int Digits = 500; boost::multiprecision::backends::digit_base_type DigitBase = boost::multiprecision::backends::digit_base_10; Allocator = void; Exponent = int; Exponent MinExponent = 0; Exponent MaxExponent = 0]'  
  121 |    cpp_bin_float(const cpp_bin_float& o) BOOST_MP_NOEXCEPT_IF(noexcept(rep_type(std::declval<const rep_type&>())))  
      |    ^~~~~~~~~~~~~  
../boost/boost/multiprecision/cpp_bin_float.hpp:121:39: note:   no known conversion for argument 1 from 'size_t' {aka 'long unsigned int'} to 'const boost::multiprecision::backends::cpp_bin_float<500>&'  
  121 |    cpp_bin_float(const cpp_bin_float& o) BOOST_MP_NOEXCEPT_IF(noexcept(rep_type(std::declval<const rep_type&>())))  
      |                  ~~~~~~~~~~~~~~~~~~~~~^  
../boost/boost/multiprecision/cpp_bin_float.hpp:119:4: note: candidate: 'boost::multiprecision::backends::cpp_bin_float<Digits, DigitBase, Allocator, Exponent, MinExponent, MaxExponent>::cpp_bin_float() [with unsigned int Digits = 500; boost::multiprecision::backends::digit_base_type DigitBase = boost::multiprecision::backends::digit_base_10; Allocator = void; Exponent = int; Exponent MinExponent = 0; Exponent MaxExponent = 0]'  
  119 |    cpp_bin_float() BOOST_MP_NOEXCEPT_IF(noexcept(rep_type())) : m_data(), m_exponent(exponent_zero), m_sign(false) {}  
      |    ^~~~~~~~~~~~~  
../boost/boost/multiprecision/cpp_bin_float.hpp:119:4: note:   candidate expects 0 arguments, 1 provided  
```

---

## Comment 1

> Username: NAThompson  
> Created at: 2020-10-14 17:52:08 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/272#issuecomment-708562088  

Sorry, always forget to use `boost::multiprecision::number` . . .
