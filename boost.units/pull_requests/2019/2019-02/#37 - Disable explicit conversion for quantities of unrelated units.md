# #37 Disable explicit conversion for quantities of unrelated units [Closed]

> Username: kaihowl  
> Created at: 2019-02-22 17:07:05 UTC  
> Updated at: 2020-06-14 11:24:47 UTC  
> Closed at: 2020-06-14 11:24:47 UTC  
> Url: https://github.com/boostorg/units/pull/37  

This fixes boostorg/units#36.

---

## Comment 1

> Username: kaihowl  
> Created_at: 2019-02-22 17:41:32 UTC  
> Url: https://github.com/boostorg/units/pull/37#issuecomment-466482077  

It seems that the windows builds are not running on a PR. It this on purpose? Should I look at them more closely?

---

## Comment 2

> Username: swatanabe  
> Created_at: 2019-02-22 23:26:04 UTC  
> Url: https://github.com/boostorg/units/pull/37#issuecomment-466583667  

AMDG  
  
This is a bit more complex than it needs to be.  
has_conversion_helper uses SFINAE to get a bool  
and then you pass the bool to enable_if to do  
more SFINAE.  
  
It's simpler to use SFINAE once:  
  
template<class Q1, class Q2>  
using require_conversion_factor =  
  void_t<decltype(conversion_factor(Q1{}, Q2{}))>;  
  
template<class Q1, class Q2>  
using require_conversion_helper =  
  void_t<decltype(conversion_helper<Q1, Q2>::convert(Q1{}))>  
  
This of course, only works in C++11, but I think  
you need to make it conditional anyway, to avoid  
the possibility of errors from over-eager evaluation  
of the SFINAE test.  
  
In Christ,  
Steven Watanabe

---

## Comment 3

> Username: kaihowl  
> Created_at: 2019-02-25 10:06:58 UTC  
> Url: https://github.com/boostorg/units/pull/37#issuecomment-466952025  

This solution was meant to work on C++03 as well. I do see that Travis does not like it anyway.  
  
When I use the proposed solution by you, I assume that `BOOST_NO_CXX11_DECLTYPE` should be used to wrapped this then?

---

## Comment 4

> Username: swatanabe  
> Created_at: 2019-02-25 15:55:40 UTC  
> Url: https://github.com/boostorg/units/pull/37#issuecomment-467065440  

AMDG  
  
On 2/25/19 3:06 AM, Kai Howelmeyer wrote:  
> This solution was meant to work on C++03 as well. I do see that Travis does not like it anyway.  
>   
  
To make it work in C++03, you need to avoid SFINAE on expressions.  
  
// limit the scope of the extra overload  
namespace has_conversion_factor_impl  
{  
// a fallback overload that will be found only  
// if no other overload of conversion_factor exists.  
struct no_conversion_factor {};  
no_conversion_factor conversion_factor(...);  
// identify whether the fallback was used.  
// conversion_factor should never return void,  
// so we don't need `operator,` magic.  
no& test(no_conversion_factor);  
yes& test(...);  
template<class Q1, class Q2>  
struct has_conversion_factor  
{  
  enum { value = sizeof((test)(conversion_factor(  
    declval<Q1>(), declval<Q2>()))) == sizeof(yes) };  
};  
}  
using has_conversion_factor_impl::has_conversion_factor;  
  
(Bleh.)  
  
has_conversion_helper can be handled by modifying the  
primary template of conversion_helper to make it possible  
to identify:  
  
template<class Q1, class Q2>  
struct conversion_helper  
{  
  typedef void _is_primary_template;  
};  
  
template<class Q1, class Q2, class = void>  
struct has_conversion_helper : true_type {};  
template<class Q1, class Q2>  
struct has_conversion_helper<Q1, Q1,  
  typename conversion_helper<Q1, Q2>::_is_primary_template>  
 : false_type {};  
  
> When I use the proposed solution by you, I assume that `BOOST_NO_CXX11_DECLTYPE` should be used to wrapped this then?  
>   
  
and BOOST_NO_SFINAE_EXPR and BOOST_NO_CXX11_TEMPLATE_ALIASES.  
  
In Christ,  
Steven Watanabe

---

## Comment 5

> Username: kaihowl  
> Created_at: 2019-03-07 15:17:24 UTC  
> Url: https://github.com/boostorg/units/pull/37#issuecomment-470566197  

How would you attach the template type alias in the C++11 version for `require_conversion_factor` to the `conversion_helper` template? If I attach it as a default template parameter argument to the class, I get an ambiguity for absolute units. If I attach it to the `convert` function, I cannot do SFINAE anymore for the `require_conversion_helper` as this is not the immediate context anymore where the substitution fails. I am stuck. (Yet again.)

---

## Comment 6

> Username: swatanabe  
> Created_at: 2019-03-07 16:06:49 UTC  
> Url: https://github.com/boostorg/units/pull/37#issuecomment-470586401  

AMDG  
  
On 3/7/19 8:17 AM, Kai Howelmeyer wrote:  
> How would you attach the template type alias in the C++11 version for `require_conversion_factor` to the `conversion_helper` template? If I attach it as a default template parameter argument to the class, I get an ambiguity for absolute units. If I attach it to the `convert` function, I cannot do SFINAE anymore for the `require_conversion_helper` as this is not the immediate context anymore where the substitution fails. I am stuck. (Yet again.)  
>   
  
You need either C++17 void_t:  
  template<class...>  
  using void_t = void;  
(depends on CWG 1558)  
  
or extra indirection (make conversion_helper  
inherit from another template and put the SFINAE  
there.)  
  
In Christ,  
Steven Watanabe

---
