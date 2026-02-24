# #187 - g++-9 on Mac: Incorrect config? [Closed]

> Username: NAThompson  
> Created at: 2020-01-27 23:22:23 UTC  
> Updated at: 2020-01-28 22:49:13 UTC  
> Closed at: 2020-01-28 22:49:13 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/187  

Given:  
  
```  
#include <iostream>  
#include <boost/multiprecision/float128.hpp>  
  
int main() {  
    #ifdef BOOST_HAS_FLOAT128  
    using boost::multiprecision::float128;  
    float128 r = 1.0Q;  
    std::cout << r << "\n";  
    #else  
    std::cout << "No float128\n";  
    #endif  
}  
```  
  
And given:  
  
```  
$ g++-9 --version  
g++-9 (Homebrew GCC 9.2.0_3) 9.2.0  
Copyright (C) 2019 Free Software Foundation, Inc.  
This is free software; see the source for copying conditions.  There is NO  
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  
```  
  
and compiling with:  
  
```  
$ g++-9  -Wfatal-errors --std=c++17 -fext-numeric-literals reproduce.cpp  -lquadmath  
```  
  
on top of boost.config develop and boost.multiprecision develop, I get the output:  
  
```  
No float128  
```  
  
If I define:  
  
```  
$ g++-9  -DBOOST_HAS_FLOAT128 -Wfatal-errors --std=c++17 -fext-numeric-literals reproduce.cpp  -lquadmath  
```  
  
I get:  
  
```  
In file included from ../../boost/container_hash/extensions.hpp:24,  
                 from ../../boost/container_hash/hash.hpp:761,  
                 from ../../boost/functional/hash.hpp:6,  
                 from ../../boost/multiprecision/float128.hpp:11,  
                 from ./include/boost/math/special_functions/daubechies_scaling.hpp:14,  
                 from daubechies_roots.cpp:5:  
../../boost/multiprecision/detail/number_base.hpp: In instantiation of 'struct boost::multiprecision::detail::bits_of<__float128>':  
../../boost/multiprecision/detail/number_base.hpp:288:81:   required from 'struct boost::multiprecision::detail::canonical_imp<__float128, boost::multiprecision::backends::float128_backend, mpl_::int_<2> >'  
../../boost/multiprecision/detail/number_base.hpp:320:65:   required from 'struct boost::multiprecision::detail::canonical<__float128, boost::multiprecision::backends::float128_backend>'  
../../boost/multiprecision/number.hpp:59:159:   required by substitution of 'template<class V> constexpr boost::multiprecision::number<boost::multiprecision::backends::float128_backend, boost::multiprecision::et_off>::number(const V&, typename boost::enable_if_c<(((((boost::is_arithmetic<From>::value || boost::is_same<std::__cxx11::basic_string<char>, V>::value) || boost::is_convertible<Val, const char*>::value) && (! boost::is_convertible<typename boost::multiprecision::detail::canonical<V, boost::multiprecision::backends::float128_backend>::type, boost::multiprecision::backends::float128_backend>::value)) && (! boost::multiprecision::detail::is_restricted_conversion<typename boost::multiprecision::detail::canonical<V, boost::multiprecision::backends::float128_backend>::type, boost::multiprecision::backends::float128_backend>::value)) && (! boost::is_same<V, __float128>::value)), void>::type*) [with V = __float128]'  
daubechies_roots.cpp:41:18:   required from here  
../../boost/multiprecision/detail/number_base.hpp:216:67: error: static assertion failed: is_integral<T>::value || is_enum<T>::value || std::numeric_limits<T>::is_specialized  
  216 |    BOOST_STATIC_ASSERT(is_integral<T>::value || is_enum<T>::value || std::numeric_limits<T>::is_specialized);  
      |                                        ~~~~~~~~~~~~~~~~~~~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
../../boost/static_assert.hpp:70:55: note: in definition of macro 'BOOST_STATIC_ASSERT'  
   70 | #     define BOOST_STATIC_ASSERT( ... ) static_assert(__VA_ARGS__, #__VA_ARGS__)  
      |                                                       ^~~~~~~~~~~  
compilation terminated due to -Wfatal-errors.  
```  
  
If I comment out the static assert on line 216 of `number_base.hpp`, everything works fine.

---

## Comment 1

> Username: jzmaddock  
> Created at: 2020-01-28 12:05:59 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/187#issuecomment-579214563  

The basic issue is this: we have no way of detecting whether __float128 support is enabled, unless you build with -std=gnu++XX.  Using -fext-numeric-literals doesn't turn on any of the defines we need to detect support.  
  
BTW if you look at the details of that trait - if the static_assert is triggered then it's logic will definitely *not* be correct.  
  
This issue does keep coming up, and I wish we could do better, but at present I'm not sure how :(

---

## Comment 2

> Username: jzmaddock  
> Created at: 2020-01-28 12:06:32 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/187#issuecomment-579214751  

Forgot to say.... we do document that -std=gnu++XX is a basic requirement.

---

## Comment 3

> Username: NAThompson  
> Created at: 2020-01-28 22:49:09 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/187#issuecomment-579504246  

@jzmaddock : I'm pretty sure this is the third time I've forgot about the `--std=gnu++XX` requirement, and resubmitted this same bug. My bad!
