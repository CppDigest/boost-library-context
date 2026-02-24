# #62 For MSVC2017 use detector_t instead of make_void [Merged]

> Username: glenfe  
> Created at: 2018-02-07 00:52:46 UTC  
> Updated at: 2018-02-10 11:59:38 UTC  
> Merged at: 2018-02-10 11:59:38 UTC  
> Closed at: 2018-02-10 11:59:38 UTC  
> Url: https://github.com/boostorg/type_traits/pull/62  

Since all these new traits require absence of BOOST_NO_CXX11_TEMPLATE_ALIASES anyway, we can just use void_t over make_void, and this also results in MSVC2017 not complaining with more complicated expressions.

---

## Comment 1

> Username: glenfe  
> Created_at: 2018-02-07 14:52:03 UTC  
> Url: https://github.com/boostorg/type_traits/pull/62#issuecomment-363792735  

Instead of void_t specifically, we use a new non-variadic alias for VC12's sake (it blows up on the variadic void_t).  
  
Every VC requires special attention as always, it seems.

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2018-02-08 12:59:56 UTC  
> Url: https://github.com/boostorg/type_traits/pull/62#issuecomment-364104430  

That's much better, given:  
  
```  
template <class T>  
using oper_equal_to_test = decltype(std::declval<T>() == std::declval<T>());  
  
template <class T>  
using has_operator_equal_to = boost::is_detected<oper_equal_to_test, T>;  
  
  
   static_assert(has_operator_equal_to<Something>::value, "oops");  
```  
  
That's basically working with vc14.x, though it gives the wrong answer for:  
  
```  
struct private_op  
{  
   private_op();  
private:  
   bool operator == (const private_op&)const;  
};  
```  
  
Where as GCC gets it right, as does msvc if you use the detection idiom directly.  
  
For vc12, any expression that generates a compile error fails to build when used with the new is_detected, so for example:  
  
```  
struct bad{};  
  
   static_assert(!has_operator_equal_to<bad>::value, "oops");  
```  
  
fails to compile.  
  
It may be we've reached the limit of what we can achieve though...

---

## Comment 3

> Username: glenfe  
> Created_at: 2018-02-08 17:05:55 UTC  
> Url: https://github.com/boostorg/type_traits/pull/62#issuecomment-364179876  

> Where as GCC gets it right, as does msvc if you use the detection idiom directly.  
  
Which version of MSVC2017 gets it right if you use the idiom directly? I tried 19.10.25017 and it gets it wrong both with the idiom directly and the through the toolkit.

---

## Comment 4

> Username: eldiener  
> Created_at: 2018-02-08 17:10:21 UTC  
> Url: https://github.com/boostorg/type_traits/pull/62#issuecomment-364181207  

Although it does not help for what now exists as MSVC2017, if you report this to Microsoft as a bug, maybe they will fix it in some upcoming release.

---

## Comment 5

> Username: jzmaddock  
> Created_at: 2018-02-08 18:11:06 UTC  
> Url: https://github.com/boostorg/type_traits/pull/62#issuecomment-364199635  

>Which version of MSVC2017 gets it right if you use the idiom directly? I tried 19.10.25017 and it gets it wrong both with the idiom directly and the through the toolkit.  
  
Please forgive the macros, but this is extracted from an updated verison of the binary operator traits and tests:  
  
```  
#include <boost/type_traits/integral_constant.hpp>  
#include <boost/type_traits/make_void.hpp>  
#include <boost/type_traits/is_convertible.hpp>  
#include <boost/type_traits/is_void.hpp>  
#include <boost/type_traits/add_reference.hpp>  
#include <utility>  
  
#define BOOST_TT_TRAIT_NAME has_equal_to  
#define BOOST_TT_TRAIT_OP ==  
  
namespace boost  
{  
  
   namespace binary_op_detail {  
  
      struct dont_care;  
  
      template <class T, class U, class Ret, class = boost::void_t<>>  
      struct BOOST_JOIN(BOOST_TT_TRAIT_NAME, _ret_imp) : public boost::false_type {};  
  
      template <class T, class U, class Ret>  
      struct BOOST_JOIN(BOOST_TT_TRAIT_NAME, _ret_imp)<T, U, Ret, boost::void_t<decltype(std::declval<typename add_reference<T>::type>() BOOST_TT_TRAIT_OP std::declval<typename add_reference<U>::type>())> >  
         : public boost::integral_constant<bool, ::boost::is_convertible<decltype(std::declval<typename add_reference<T>::type>() BOOST_TT_TRAIT_OP std::declval<typename add_reference<U>::type>()), Ret>::value> {};  
  
      template <class T, class U, class = boost::void_t<> >  
      struct BOOST_JOIN(BOOST_TT_TRAIT_NAME, _void_imp) : public boost::false_type {};  
  
      template <class T, class U>  
      struct BOOST_JOIN(BOOST_TT_TRAIT_NAME, _void_imp)<T, U, boost::void_t<decltype(std::declval<typename add_reference<T>::type>() BOOST_TT_TRAIT_OP std::declval<typename add_reference<U>::type>())> >  
         : public boost::integral_constant<bool, ::boost::is_void<decltype(std::declval<typename add_reference<T>::type>() BOOST_TT_TRAIT_OP std::declval<typename add_reference<U>::type>())>::value> {};  
  
      template <class T, class U, class = boost::void_t<>>  
      struct BOOST_JOIN(BOOST_TT_TRAIT_NAME, _dc_imp) : public boost::false_type {};  
  
      template <class T, class U>  
      struct BOOST_JOIN(BOOST_TT_TRAIT_NAME, _dc_imp)<T, U, boost::void_t<decltype(std::declval<typename add_reference<T>::type>() BOOST_TT_TRAIT_OP std::declval<typename add_reference<U>::type>())> >  
         : public boost::true_type {};  
  
   }  
  
   template <class T, class U = T, class Ret = boost::binary_op_detail::dont_care>  
   struct BOOST_TT_TRAIT_NAME : public boost::binary_op_detail::BOOST_JOIN(BOOST_TT_TRAIT_NAME, _ret_imp) <T, U, Ret> {};  
   template <class T, class U>  
   struct BOOST_TT_TRAIT_NAME<T, U, void> : public boost::binary_op_detail::BOOST_JOIN(BOOST_TT_TRAIT_NAME, _void_imp) <T, U> {};  
   template <class T, class U>  
   struct BOOST_TT_TRAIT_NAME<T, U, boost::binary_op_detail::dont_care> : public boost::binary_op_detail::BOOST_JOIN(BOOST_TT_TRAIT_NAME, _dc_imp) <T, U> {};  
  
  
}  
  
struct private_op { private: bool operator == (const private_op&) { return false; } };  
  
struct ambiguous_A  
{  
};  
inline bool operator == (const ambiguous_A&, const ambiguous_A&) { return true; }  
struct ambiguous_B { operator ambiguous_A()const { return ambiguous_A(); } };  
  
  
  
int main()  
{  
   static_assert(!boost::has_equal_to<private_op>::value, "oops");  
     
   static_assert(boost::has_equal_to<ambiguous_A>::value, "oops");  
   static_assert(boost::has_equal_to<ambiguous_B>::value, "oops");  
}  
```  
  
Which works for me with VS2017 15.5.4, _MSC_FULL_VER=191225834, not with 14.0 though.

---

## Comment 6

> Username: jzmaddock  
> Created_at: 2018-02-08 18:11:36 UTC  
> Url: https://github.com/boostorg/type_traits/pull/62#issuecomment-364199769  

>Although it does not help for what now exists as MSVC2017, if you report this to Microsoft as a bug, maybe they will fix it in some upcoming release.  
  
Nod

---

## Comment 7

> Username: glenfe  
> Created_at: 2018-02-08 18:45:58 UTC  
> Url: https://github.com/boostorg/type_traits/pull/62#issuecomment-364209629  

> Which works for me with VS2017 15.5.4, _MSC_FULL_VER=191225834, not with 14.0 though.  
  
Ah, the 19.12 explains it: They have fixed this since 19.10. This private member case was what made MSVC fail to meet the requirements of BOOST_NO_CXX11_SFINAE_EXPR (that I added when I was updating boost::addressof).   
  
That they didn't fix things enough is regrettable (enough to make it work through the toolkit too). I will keep looking for more workarounds in the meanwhile.

---

## Comment 8

> Username: jzmaddock  
> Created_at: 2018-02-08 18:54:09 UTC  
> Url: https://github.com/boostorg/type_traits/pull/62#issuecomment-364212075  

Found while testing other stuff with gcc-5.1.0:  
  
```  
../../../bin.v2/libs/type_traits/test/is_detected_exact_test.test/gcc-5.1.0s14/d  
ebug/is_detected_exact_test.o: In function `main':  
/home/jzm/boost/boost/libs/type_traits/test/is_detected_exact_test.cpp:38: undef  
ined reference to `boost::is_detected_exact_v<char, type_t, int>'  
/home/jzm/boost/boost/libs/type_traits/test/is_detected_exact_test.cpp:38: undef  
ined reference to `boost::is_detected_exact_v<char, type_t, int>'  
/home/jzm/boost/boost/libs/type_traits/test/is_detected_exact_test.cpp:39: undef  
ined reference to `boost::is_detected_exact_v<char, type_t, has_type>'  
/home/jzm/boost/boost/libs/type_traits/test/is_detected_exact_test.cpp:39: undef  
ined reference to `boost::is_detected_exact_v<char, type_t, has_type>'  
/home/jzm/boost/boost/libs/type_traits/test/is_detected_exact_test.cpp:40: undef  
ined reference to `boost::is_detected_exact_v<char, type_t, no_type>'  
/home/jzm/boost/boost/libs/type_traits/test/is_detected_exact_test.cpp:40: undef  
ined reference to `boost::is_detected_exact_v<char, type_t, no_type>'  
collect2: error: ld returned 1 exit status  
```  
  
Likewise the _exact and _convertible versions.

---

## Comment 9

> Username: glenfe  
> Created_at: 2018-02-08 18:59:38 UTC  
> Url: https://github.com/boostorg/type_traits/pull/62#issuecomment-364213688  

The GCC 5.1 bug that was fixed in 5.2?  https://gcc.gnu.org/bugzilla/show_bug.cgi?id=65719

---

## Comment 10

> Username: jzmaddock  
> Created_at: 2018-02-08 19:06:46 UTC  
> Url: https://github.com/boostorg/type_traits/pull/62#issuecomment-364215666  

>The GCC 5.1 bug that was fixed in 5.2  
  
Yes.

---
