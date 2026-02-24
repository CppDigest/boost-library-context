# #194 - Pessimizing move warning: [Closed]

> Username: NAThompson  
> Created at: 2020-02-08 17:33:00 UTC  
> Updated at: 2020-03-31 08:32:27 UTC  
> Closed at: 2020-03-31 08:32:27 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/194  

With g++-9, we have the following warning:  
  
```  
../../boost/multiprecision/detail/number_base.hpp:160:39: warning: moving a local object in a return statement prevents copy elision [-Wpessimizing-move]  
  160 | #  define BOOST_MP_MOVE(x) std::move(x)  
      |                                       ^  
../../boost/multiprecision/detail/default_ops.hpp:3567:11: note: in expansion of macro 'BOOST_MP_MOVE'  
 3567 |    return BOOST_MP_MOVE(result);  
      |           ^~~~~~~~~~~~~  
../../boost/multiprecision/detail/number_base.hpp:160:39: note: remove 'std::move' call  
  160 | #  define BOOST_MP_MOVE(x) std::move(x)  
      |                                       ^  
../../boost/multiprecision/detail/default_ops.hpp:3567:11: note: in expansion of macro 'BOOST_MP_MOVE'  
 3567 |    return BOOST_MP_MOVE(result);  
      |           ^~~~~~~~~~~~~  
```  
  
Low priority, obviously, but it's a bit verbose with all the et stuff spat out with it.

---

## Comment 1

> Username: madhur4127  
> Created at: 2020-03-29 11:27:28 UTC  
> Updated at: 2020-03-29 11:27:35 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/194#issuecomment-605621671  

I think this requires some benchmarking on different compilers to check whether `copy elision` has greater benefits than move semantics. Accordingly, the [docs will have to be updated](https://www.boost.org/doc/libs/1_72_0/libs/multiprecision/doc/html/boost_multiprecision/intro.html#boost_multiprecision.intro.move_semantics) if it is found faster.

---

## Comment 2

> Username: jzmaddock  
> Created at: 2020-03-29 13:19:26 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/194#issuecomment-605634511  

No it's a bug: when Multiprecision was being written rvalue refs were literally brand new, I think now it's fairly well understood that "named return value optimization" also beats move-return.  
  
However, I have some recollection of fixing this - can anyone check and see if the warnings are still there for current develop?  I might have missed something of course...

---

## Comment 3

> Username: NAThompson  
> Created at: 2020-03-29 13:50:13 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/194#issuecomment-605638863  

I checked: The macro is still there:  
  
```cpp  
//  
// Move support:  
//  
#ifndef BOOST_NO_CXX11_RVALUE_REFERENCES  
#define BOOST_MP_MOVE(x) std::move(x)  
#else  
#define BOOST_MP_MOVE(x) x  
#endif  
```  
  
and grepping for `return BOOST_MP_MOVE(` gives quite a few hits:  
  
```  
$ grep -nr 'return BOOST_MP_MOVE' include/  
include//boost/multiprecision/detail/min_max.hpp:39:   return BOOST_MP_MOVE(t);  
include//boost/multiprecision/detail/min_max.hpp:46:      return BOOST_MP_MOVE(t);  
include//boost/multiprecision/detail/min_max.hpp:54:      return BOOST_MP_MOVE(t1);  
include//boost/multiprecision/detail/min_max.hpp:55:   return BOOST_MP_MOVE(t2);  
include//boost/multiprecision/detail/min_max.hpp:62:      return BOOST_MP_MOVE(t1);  
include//boost/multiprecision/detail/min_max.hpp:63:   return BOOST_MP_MOVE(t2);  
include//boost/multiprecision/detail/min_max.hpp:77:   return BOOST_MP_MOVE(t);  
include//boost/multiprecision/detail/min_max.hpp:84:      return BOOST_MP_MOVE(t);  
include//boost/multiprecision/detail/min_max.hpp:92:      return BOOST_MP_MOVE(t1);  
include//boost/multiprecision/detail/min_max.hpp:93:   return BOOST_MP_MOVE(t2);  
include//boost/multiprecision/detail/min_max.hpp:100:      return BOOST_MP_MOVE(t1);  
include//boost/multiprecision/detail/min_max.hpp:101:   return BOOST_MP_MOVE(t2);  
include//boost/multiprecision/detail/default_ops.hpp:2052:   return BOOST_MP_MOVE(boost::math::hypot(real(v), imag(v)));  
include//boost/multiprecision/detail/default_ops.hpp:2059:   return BOOST_MP_MOVE(abs(static_cast<number_type>(v)));  
include//boost/multiprecision/detail/default_ops.hpp:2066:   return BOOST_MP_MOVE(atan2(imag(v), real(v)));  
include//boost/multiprecision/detail/default_ops.hpp:2079:   return BOOST_MP_MOVE(arg(static_cast<number_type>(v)));  
include//boost/multiprecision/detail/default_ops.hpp:2087:   return BOOST_MP_MOVE(a * a + b * b);  
include//boost/multiprecision/detail/default_ops.hpp:2100:   return BOOST_MP_MOVE(norm(static_cast<number_type>(v)));  
include//boost/multiprecision/detail/default_ops.hpp:2489:   return BOOST_MP_MOVE(trunc(number_type(v), pol));  
include//boost/multiprecision/detail/default_ops.hpp:2589:   return BOOST_MP_MOVE(round(static_cast<number_type>(v), pol));  
include//boost/multiprecision/detail/default_ops.hpp:2703:   return BOOST_MP_MOVE(frexp(static_cast<number_type>(v), pint));  
include//boost/multiprecision/detail/default_ops.hpp:2719:   return BOOST_MP_MOVE(frexp(static_cast<number_type>(v), pint));  
include//boost/multiprecision/detail/default_ops.hpp:2735:   return BOOST_MP_MOVE(frexp(static_cast<number_type>(v), pint));  
include//boost/multiprecision/detail/default_ops.hpp:2751:   return BOOST_MP_MOVE(frexp(static_cast<number_type>(v), pint));  
include//boost/multiprecision/detail/default_ops.hpp:3591:   return BOOST_MP_MOVE(detail::sinc_pi_imp(x));  
include//boost/multiprecision/detail/default_ops.hpp:3598:   return BOOST_MP_MOVE(detail::sinc_pi_imp(x));  
include//boost/multiprecision/detail/default_ops.hpp:3605:   return BOOST_MP_MOVE(detail::sinhc_pi_imp(x));  
include//boost/multiprecision/detail/default_ops.hpp:3612:   return BOOST_MP_MOVE(boost::math::sinhc_pi(x));  
```

---

## Comment 4

> Username: jzmaddock  
> Created at: 2020-03-29 17:39:23 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/194#issuecomment-605672411  

>I checked: The macro is still there:  
  
There's nothing wrong with the macro or it's use as such, it's *how* it's used that matters: grepping through I don't see anything that would be eligible for named-return-value-optimisation.  
  
However, I'm not sure whether code like this:  
  
```  
template <class tag, class A1, class A2, class A3, class A4>  
inline BOOST_MP_CXX14_CONSTEXPR typename enable_if_c<number_category<typename detail::expression<tag, A1, A2, A3, A4>::result_type>::value == number_kind_floating_point, typename detail::expression<tag, A1, A2, A3, A4>::result_type>::type  
frexp(const detail::expression<tag, A1, A2, A3, A4>& v, short* pint)  
{  
   typedef typename detail::expression<tag, A1, A2, A3, A4>::result_type number_type;  
   return BOOST_MP_MOVE(frexp(static_cast<number_type>(v), pint));  
}  
```  
  
Is eligible for copy-elision, and so generates a warning?

---

## Comment 5

> Username: jzmaddock  
> Created at: 2020-03-31 08:32:21 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/194#issuecomment-606480078  

Just ran the tests with gcc-9 and clang-9 and warnings=all and found none of these warnings, so closing down for now.... please reopen if there's a reproducer!
