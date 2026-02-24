# #287 - Warnings about implicit generation of operator= being deprecated [Closed]

> Username: Lastique  
> Created at: 2021-01-11 12:26:38 UTC  
> Updated at: 2021-06-02 09:53:26 UTC  
> Closed at: 2021-06-02 09:53:26 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/287  

In C++11 mode, gcc-10 emits the following warnings when building Boost.Integer tests:  
  
```  
gcc.compile.c++ ../../../bin.v2/libs/integer/test/extended_euclidean_test.test/gcc-10/release/cxxstd-11-iso/threading-multi/visibility-hidden/extended_euclidean_test.o  
In file included from ../../../boost/config.hpp:61,  
                 from multiprecision_config.hpp:10,  
                 from extended_euclidean_test.cpp:8:  
../../../boost/multiprecision/cpp_int.hpp: In instantiation of ‘boost::multiprecision::backends::cpp_int_backend<MinBits, MaxBits, SignType, Checked, Allocator>& boost::multiprecision::backends::cpp_int_backend<MinBits, MaxBits, SignType, Checked, Allocator>::operator=(boost::multiprecision::backends::cpp_int_backend<MinBits, MaxBits, SignType, Checked, Allocator>&&) [with unsigned int MinBits = 128; unsigned int MaxBits = 128; boost::multiprecision::cpp_integer_type SignType = boost::multiprecision::signed_magnitude; boost::multiprecision::cpp_int_check_type Checked = boost::multiprecision::unchecked; Allocator = void]’:  
../../../boost/multiprecision/number.hpp:321:80:   required from ‘boost::multiprecision::number<Backend, ExpressionTemplates>& boost::multiprecision::number<Backend, ExpressionTemplates>::operator=(boost::multiprecision::number<Backend, ExpressionTemplates>&&) [with Backend = boost::multiprecision::backends::cpp_int_backend<128, 128, boost::multiprecision::signed_magnitude, boost::multiprecision::unchecked, void>; boost::multiprecision::expression_template_option ExpressionTemplates = boost::multiprecision::et_off]’  
../../../boost/integer/extended_euclidean.hpp:55:12:   required from ‘typename boost::enable_if_c<std::numeric_limits<_Tp>::is_signed, boost::integer::euclidean_result_t<Z> >::type boost::integer::extended_euclidean(Z, Z) [with Z = boost::multiprecision::number<boost::multiprecision::backends::cpp_int_backend<128, 128, boost::multiprecision::signed_magnitude, boost::multiprecision::unchecked, void> >; typename boost::enable_if_c<std::numeric_limits<_Tp>::is_signed, boost::integer::euclidean_result_t<Z> >::type = boost::integer::euclidean_result_t<boost::multiprecision::number<boost::multiprecision::backends::cpp_int_backend<128, 128, boost::multiprecision::signed_magnitude, boost::multiprecision::unchecked, void> > >]’  
extended_euclidean_test.cpp:32:73:   required from ‘void test_extended_euclidean() [with Z = boost::multiprecision::number<boost::multiprecision::backends::cpp_int_backend<128, 128, boost::multiprecision::signed_magnitude, boost::multiprecision::unchecked, void> >]’  
extended_euclidean_test.cpp:49:39:   required from here  
../../../boost/multiprecision/cpp_int.hpp:1478:155: warning: implicitly-declared ‘boost::multiprecision::backends::cpp_int_base<128, 128, boost::multiprecision::signed_magnitude, boost::multiprecision::unchecked, void, true>& boost::multiprecision::backends::cpp_int_base<128, 128, boost::multiprecision::signed_magnitude, boost::multiprecision::unchecked, void, true>::operator=(const boost::multiprecision::backends::cpp_int_base<128, 128, boost::multiprecision::signed_magnitude, boost::multiprecision::unchecked, void, true>&)’ is deprecated [-Wdeprecated-copy]  
 1478 |    BOOST_MP_FORCEINLINE BOOST_MP_CXX14_CONSTEXPR cpp_int_backend& operator=(cpp_int_backend&& o) BOOST_MP_NOEXCEPT_IF(noexcept(std::declval<base_type&>() = std::declval<base_type>()))  
      |                                                                                                                                ~~~~~~~~~~~~~~~~~~~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~~  
../../../boost/multiprecision/cpp_int.hpp:1478:98: note: in expansion of macro ‘BOOST_MP_NOEXCEPT_IF’  
 1478 |    BOOST_MP_FORCEINLINE BOOST_MP_CXX14_CONSTEXPR cpp_int_backend& operator=(cpp_int_backend&& o) BOOST_MP_NOEXCEPT_IF(noexcept(std::declval<base_type&>() = std::declval<base_type>()))  
      |                                                                                                  ^~~~~~~~~~~~~~~~~~~~  
In file included from extended_euclidean_test.cpp:14:  
../../../boost/multiprecision/cpp_int.hpp:1103:41: note: because ‘boost::multiprecision::backends::cpp_int_base<128, 128, boost::multiprecision::signed_magnitude, boost::multiprecision::unchecked, void, true>’ has user-provided ‘constexpr boost::multiprecision::backends::cpp_int_base<MinBits, MinBits, boost::multiprecision::signed_magnitude, Checked, void, true>::cpp_int_base(const boost::multiprecision::backends::cpp_int_base<MinBits, MinBits, boost::multiprecision::signed_magnitude, Checked, void, true>&) [with unsigned int MinBits = 128; boost::multiprecision::cpp_int_check_type Checked = boost::multiprecision::unchecked]’  
 1103 |    BOOST_MP_FORCEINLINE BOOST_CONSTEXPR cpp_int_base(const cpp_int_base& o) BOOST_NOEXCEPT  
      |                                         ^~~~~~~~~~~~  
../../../boost/multiprecision/cpp_int.hpp: In instantiation of ‘boost::multiprecision::backends::cpp_int_backend<MinBits, MaxBits, SignType, Checked, Allocator>& boost::multiprecision::backends::cpp_int_backend<MinBits, MaxBits, SignType, Checked, Allocator>::operator=(boost::multiprecision::backends::cpp_int_backend<MinBits, MaxBits, SignType, Checked, Allocator>&&) [with unsigned int MinBits = 128; unsigned int MaxBits = 128; boost::multiprecision::cpp_integer_type SignType = boost::multiprecision::signed_magnitude; boost::multiprecision::cpp_int_check_type Checked = boost::multiprecision::unchecked; Allocator = void]’:  
../../../boost/multiprecision/number.hpp:323:17:   required from ‘boost::multiprecision::number<Backend, ExpressionTemplates>& boost::multiprecision::number<Backend, ExpressionTemplates>::operator=(boost::multiprecision::number<Backend, ExpressionTemplates>&&) [with Backend = boost::multiprecision::backends::cpp_int_backend<128, 128, boost::multiprecision::signed_magnitude, boost::multiprecision::unchecked, void>; boost::multiprecision::expression_template_option ExpressionTemplates = boost::multiprecision::et_off]’  
../../../boost/integer/extended_euclidean.hpp:55:12:   required from ‘typename boost::enable_if_c<std::numeric_limits<_Tp>::is_signed, boost::integer::euclidean_result_t<Z> >::type boost::integer::extended_euclidean(Z, Z) [with Z = boost::multiprecision::number<boost::multiprecision::backends::cpp_int_backend<128, 128, boost::multiprecision::signed_magnitude, boost::multiprecision::unchecked, void> >; typename boost::enable_if_c<std::numeric_limits<_Tp>::is_signed, boost::integer::euclidean_result_t<Z> >::type = boost::integer::euclidean_result_t<boost::multiprecision::number<boost::multiprecision::backends::cpp_int_backend<128, 128, boost::multiprecision::signed_magnitude, boost::multiprecision::unchecked, void> > >]’  
extended_euclidean_test.cpp:32:73:   required from ‘void test_extended_euclidean() [with Z = boost::multiprecision::number<boost::multiprecision::backends::cpp_int_backend<128, 128, boost::multiprecision::signed_magnitude, boost::multiprecision::unchecked, void> >]’  
extended_euclidean_test.cpp:49:39:   required from here  
../../../boost/multiprecision/cpp_int.hpp:1480:38: warning: implicitly-declared ‘boost::multiprecision::backends::cpp_int_base<128, 128, boost::multiprecision::signed_magnitude, boost::multiprecision::unchecked, void, true>& boost::multiprecision::backends::cpp_int_base<128, 128, boost::multiprecision::signed_magnitude, boost::multiprecision::unchecked, void, true>::operator=(const boost::multiprecision::backends::cpp_int_base<128, 128, boost::multiprecision::signed_magnitude, boost::multiprecision::unchecked, void, true>&)’ is deprecated [-Wdeprecated-copy]  
 1480 |       *static_cast<base_type*>(this) = static_cast<base_type&&>(o);  
      |       ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
../../../boost/multiprecision/cpp_int.hpp:1103:41: note: because ‘boost::multiprecision::backends::cpp_int_base<128, 128, boost::multiprecision::signed_magnitude, boost::multiprecision::unchecked, void, true>’ has user-provided ‘constexpr boost::multiprecision::backends::cpp_int_base<MinBits, MinBits, boost::multiprecision::signed_magnitude, Checked, void, true>::cpp_int_base(const boost::multiprecision::backends::cpp_int_base<MinBits, MinBits, boost::multiprecision::signed_magnitude, Checked, void, true>&) [with unsigned int MinBits = 128; boost::multiprecision::cpp_int_check_type Checked = boost::multiprecision::unchecked]’  
 1103 |    BOOST_MP_FORCEINLINE BOOST_CONSTEXPR cpp_int_base(const cpp_int_base& o) BOOST_NOEXCEPT  
      |                                         ^~~~~~~~~~~~  
```  
  
Implicit generation of `operator=` when a copy/move constructor is defined by user is deprecated since C++11.

---

## Comment 1

> Username: ckormanyos  
> Created at: 2021-03-06 18:51:38 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/287#issuecomment-792021066  

I can try to start looking into these for 1.77.  
  
While doing some CI in another project, I found a similar warning message (of high warning-level) on clang in boost-develop 1.76 moving toward release.  
  
But I do not think it wise to get into this kind of stuff for 1.76.  
  
The one I found is:  
  
```  
../boost-root/boost/random/detail/polynomial.hpp:303:20: warning: definition of implicit copy constructor for 'reference' is deprecated because it has a user-declared copy assignment operator [-Wdeprecated-copy]  
        reference &operator=(const reference &other)  
                   ^  
../boost-root/boost/random/detail/polynomial.hpp:315:16: note: in implicit copy constructor for 'boost::random::detail::polynomial::reference' first required here  
        return reference(_storage[i/bits], i%bits);  
               ^  
```

---

## Comment 2

> Username: jzmaddock  
> Created at: 2021-03-06 19:17:51 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/287#issuecomment-792031124  

I'm aware that there are lot's of these than need looking at: especially in Math.  But aren't those two in Boost.Random rather than here?

---

## Comment 3

> Username: ckormanyos  
> Created at: 2021-03-06 21:31:20 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/287#issuecomment-792058081  

> But aren't those two in Boost.Random rather than here  
  
Yes. My mind misinterpreted that.  
Sorry about not properly identifying that.
