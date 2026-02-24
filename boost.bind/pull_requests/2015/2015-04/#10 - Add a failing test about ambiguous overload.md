# #10 Add a failing test about ambiguous overload [Closed]

> Username: alkino  
> Created at: 2015-04-29 13:37:14 UTC  
> Updated at: 2015-04-29 17:14:17 UTC  
> Closed at: 2015-04-29 17:14:17 UTC  
> Url: https://github.com/boostorg/bind/pull/10  

The wrong commit is:  
https://github.com/boostorg/bind/commit/30921a3889f3aad1fb9456aa3417f16dcf4c23c1  
  
Failed with gcc 4.9.2 and clang 3.6.0 on linux.  
  
gcc:  
  
```  
In file included from ./boost/detail/lightweight_test.hpp:15:0,  
                 from libs/bind/test/bind_void_test.cpp:34:  
libs/bind/test/bind_void_test.cpp: In function ‘void function_test()’:  
libs/bind/test/bind_void_test.cpp:112:40: error: call of overloaded ‘bind(void (X::*)(), X)’ is ambiguous  
     BOOST_TEST( (bind<void>(&X::f0, X())(), (global_result == 1L)) );  
                                        ^  
./boost/core/lightweight_test.hpp:146:28: note: in definition of macro ‘BOOST_TEST’  
 #define BOOST_TEST(expr) ((expr)? (void)0: ::boost::detail::test_failed_impl(#expr, __FILE__, __LINE__, BOOST_CURRENT_FUNCTION))  
                            ^  
libs/bind/test/bind_void_test.cpp:112:40: note: candidates are:  
     BOOST_TEST( (bind<void>(&X::f0, X())(), (global_result == 1L)) );  
                                        ^  
./boost/core/lightweight_test.hpp:146:28: note: in definition of macro ‘BOOST_TEST’  
 #define BOOST_TEST(expr) ((expr)? (void)0: ::boost::detail::test_failed_impl(#expr, __FILE__, __LINE__, BOOST_CURRENT_FUNCTION))  
                            ^  
In file included from ./boost/bind.hpp:22:0,  
                 from libs/bind/test/bind_void_test.cpp:21:  
./boost/bind/bind.hpp:1579:20: note: boost::_bi::bind_t<R, F, typename boost::_bi::list_av_1<A1>::type> boost::bind(F, A1) [with R = void; F = void (X::*)(); A1 = X; typename boost::_bi::list_av_1<A1>::type = boost::_bi::list1<boost::_bi::value<X> >]  
 #define BOOST_BIND bind  
                    ^  
./boost/bind/bind.hpp:1594:5: note: in expansion of macro ‘BOOST_BIND’  
     BOOST_BIND(F f, A1 a1)  
     ^  
./boost/bind/bind.hpp:1579:20: note: boost::_bi::bind_t<R, boost::_mfi::mf0<R, T>, typename boost::_bi::list_av_1<A1>::type> boost::bind(R (T::*)(), A1) [with R = void; T = X; A1 = X; typename boost::_bi::list_av_1<A1>::type = boost::_bi::list1<boost::_bi::value<X> >]  
 #define BOOST_BIND bind  
                    ^  
./boost/bind/bind_mf_cc.hpp:20:5: note: in expansion of macro ‘BOOST_BIND’  
     BOOST_BIND(R (BOOST_BIND_MF_CC T::*f) (), A1 a1)  
     ^  
./boost/bind/bind.hpp:1579:20: note: boost::_bi::bind_t<Rt2, boost::_mfi::mf0<R, T>, typename boost::_bi::list_av_1<A1>::type> boost::bind(R (T::*)(), A1) [with Rt2 = void; R = void; T = X; A1 = X; typename boost::_bi::list_av_1<A1>::type = boost::_bi::list1<boost::_bi::value<X> >]  
 #define BOOST_BIND bind  
                    ^  
./boost/bind/bind_mf_cc.hpp:40:5: note: in expansion of macro ‘BOOST_BIND’  
     BOOST_BIND(R (BOOST_BIND_MF_CC T::*f) (), A1 a1)  
```

---

## Comment 1

> Username: pdimov  
> Created_at: 2015-04-29 15:09:19 UTC  
> Url: https://github.com/boostorg/bind/pull/10#issuecomment-97459508  

@K-ballo Looks like the additional `Rt2` member overloads introduce an ambiguity when the return type is already `void`.

---

## Comment 2

> Username: K-ballo  
> Created_at: 2015-04-29 15:27:47 UTC  
> Url: https://github.com/boostorg/bind/pull/10#issuecomment-97470632  

Indeed it does. I do not know how to resolve the ambiguity in C++98 terms, so I'm thinking disabling the additional overload when `Rt2 == R`. Thoughts?

---

## Comment 3

> Username: pdimov  
> Created_at: 2015-04-29 16:02:47 UTC  
> Url: https://github.com/boostorg/bind/pull/10#issuecomment-97481186  

I was thinking the same - `enable_if` or an equivalent is C++98. Nothing more elegant comes to mind.

---

## Comment 4

> Username: pdimov  
> Created_at: 2015-04-29 17:14:17 UTC  
> Url: https://github.com/boostorg/bind/pull/10#issuecomment-97508241  

Should be fixed by #11.

---
