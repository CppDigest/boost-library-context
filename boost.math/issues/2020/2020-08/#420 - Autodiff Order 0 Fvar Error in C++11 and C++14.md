# #420 - Autodiff Order 0 Fvar Error in C++11 and C++14 [Closed]

> Username: shawn-mcadam  
> Created at: 2020-08-15 23:15:01 UTC  
> Updated at: 2020-08-16 22:23:29 UTC  
> Closed at: 2020-08-16 22:23:29 UTC  
> Url: https://github.com/boostorg/math/issues/420  

When compiled with -std=c++11 or c++14 every math function in this code  
```  
#include <iostream>  
#include <boost/math/differentiation/autodiff.hpp>  
  
int main(){  
  using boost::math::differentiation::make_fvar;  
  std::cout << log(make_fvar<double,0>(1)) << std::endl;  
  std::cout << asin(make_fvar<double,0>(0)) << std::endl;  
  std::cout << asinh(make_fvar<double,0>(0)) << std::endl;  
  std::cout << tan(make_fvar<double,0>(0)) << std::endl;  
  std::cout << atan(make_fvar<double,0>(0)) << std::endl;  
  std::cout << atanh(make_fvar<double,0>(0)) << std::endl;  
  std::cout << acos(make_fvar<double,0>(0)) << std::endl;  
  std::cout << acosh(make_fvar<double,0>(1)) << std::endl;  
  std::cout << erf(make_fvar<double,0>(0)) << std::endl;  
}  
```  
produces error in g++ 10.2.0 (the specific command being `g++-10 -std=c++11 -I/usr/local/include fvar_order_0.cpp`) along the lines of  
```  
fvar_order_0.cpp:5:43:   required from here  
/usr/local/include/boost/math/differentiation/autodiff.hpp:681:81: error: too many initializers for 'std::__array_traits<double, 0>::_Type'  
  681 | fvar<RealType, Order>::fvar(RealType2 const& ca) : v{{static_cast<RealType>(ca)}} {}  
```  
and similar errors occur with the same standards in clang 11.0.3  
```  
/usr/local/include/boost/math/differentiation/autodiff.hpp:676:55: error: type 'double' cannot be narrowed to 'std::__1::array<double, 0>::_CharType' (aka 'char') in  
      initializer list [-Wc++11-narrowing]  
fvar<RealType, Order>::fvar(root_type const& ca) : v{{static_cast<RealType>(ca)}} {}  
```  
The code compiles without a problem when using -std=c++17 or c++20. I think the problem is that each of those functions has a line like  
```  
if BOOST_AUTODIFF_IF_CONSTEXPR (order == 0)  
    return fvar<RealType, Order>(d0);  
  else {  
    auto x = make_fvar<root_type, order - 1>(static_cast<root_type>(cr));  
```  
and since BOOST_AUTODIFF_IF_CONSTEXPR is null in c++11 and c++14 the compiler will try calling `make_fvar<double,-1>()`. The main problem is just that the error messages are really confusing, so I'd be happy with something along the lines of a `static_assert(ORDER != 0,"")`. Thank you!

---

## Comment 1

> Username: pulver  
> Created at: 2020-08-16 13:22:51 UTC  
> Url: https://github.com/boostorg/math/issues/420#issuecomment-674526195  

Thank you @ssm259 both for the report and diagnosis. This will be fixed shortly.

---

## Comment 2

> Username: pulver  
> Created at: 2020-08-16 14:39:23 UTC  
> Updated at: 2020-08-16 16:28:31 UTC  
> Url: https://github.com/boostorg/math/issues/420#issuecomment-674534649  

@NAThompson I just realized I probably should have submitted this via a separate branch and then requested a PR to `develop`. My apologies for any inconvenience - feel free to make any changes or delete this commit.

---

## Comment 3

> Username: NAThompson  
> Created at: 2020-08-16 14:44:15 UTC  
> Url: https://github.com/boostorg/math/issues/420#issuecomment-674535202  

@pulver : You're right that would've been better, but my feeling aren't hurt.  
  
@ssm259 : Is the issue fixed for you?

---

## Comment 4

> Username: pulver  
> Created at: 2020-08-16 14:52:45 UTC  
> Url: https://github.com/boostorg/math/issues/420#issuecomment-674536216  

> @ssm259 : Is the issue fixed for you?  
  
In case you are testing against a slightly older version of boost, you may have to manually define `BOOST_IF_CONSTEXPR` like so prior to the `#include`, which was only recently added:  
  
```  
#include <boost/config.hpp>  
//  
// C++17 if constexpr  
//  
#if !defined(BOOST_NO_CXX17_IF_CONSTEXPR)  
#  define BOOST_IF_CONSTEXPR if constexpr  
#else  
#  define BOOST_IF_CONSTEXPR if  
#endif  
#include <boost/math/differentiation/autodiff.hpp>  
```

---

## Comment 5

> Username: shawn-mcadam  
> Created at: 2020-08-16 22:23:23 UTC  
> Url: https://github.com/boostorg/math/issues/420#issuecomment-674585368  

@pulver @NAThompson commit d739449 seems to have fixed things. Thanks, guys!
