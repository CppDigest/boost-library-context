# #351 Add Feigenbaum constant. [Merged]

> Username: NAThompson  
> Created at: 2020-05-05 21:44:20 UTC  
> Updated at: 2020-06-05 01:57:42 UTC  
> Merged at: 2020-06-05 01:57:38 UTC  
> Closed at: 2020-06-05 01:57:38 UTC  
> Url: https://github.com/boostorg/math/pull/351  



---

## Comment 1

> Username: NAThompson  
> Created_at: 2020-05-06 12:14:26 UTC  
> Url: https://github.com/boostorg/math/pull/351#issuecomment-624613476  

@jzmaddock : Could you give this a quick glance? I removed some old workarounds that are pre-C++11 and want to make sure I don't break everything.

---

## Comment 2

> Username: NAThompson  
> Created_at: 2020-05-08 11:21:51 UTC  
> Url: https://github.com/boostorg/math/pull/351#issuecomment-625769177  

I now have all the concepts passing again, but one further problem with `big_real_concept`:  
  
```  
In file included from ../../boost/type_traits/is_convertible.hpp:20,  
                 from ../../boost/math/tools/promotion.hpp:29,  
                 from ../../boost/math/special_functions/detail/round_fwd.hpp:12,  
                 from ../../boost/math/special_functions/math_fwd.hpp:27,  
                 from ../../boost/math/special_functions/next.hpp:14,  
                 from test/math_unit_test.hpp:14,  
                 from test/test_constants.cpp:19:  
../../boost/math/tools/precision.hpp: In instantiation of 'constexpr T boost::math::tools::min_value() [with T = boost::math::concepts::big_real_concept]':  
../../boost/math/special_functions/next.hpp:493:89:   required from 'T boost::math::detail::float_distance_imp(const T&, const T&, const true_type&, const Policy&) [with T = boost::math::concepts::big_real_concept; Policy = boost::math::policies::policy<boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy>; boost::true_type = boost::integral_constant<bool, true>]'  
../../boost/math/special_functions/next.hpp:689:40:   required from 'typename boost::math::tools::promote_args<RT1, RT2>::type boost::math::float_distance(const T&, const U&, const Policy&) [with T = boost::math::concepts::big_real_concept; U = boost::math::concepts::big_real_concept; Policy = boost::math::policies::policy<boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy>; typename boost::math::tools::promote_args<RT1, RT2>::type = boost::math::concepts::big_real_concept]'  
../../boost/math/special_functions/next.hpp:696:38:   required from 'typename boost::math::tools::promote_args<RT1, RT2>::type boost::math::float_distance(const T&, const U&) [with T = boost::math::concepts::big_real_concept; U = boost::math::concepts::big_real_concept; typename boost::math::tools::promote_args<RT1, RT2>::type = boost::math::concepts::big_real_concept]'  
test/math_unit_test.hpp:94:48:   required from 'bool boost::math::test::check_ulp_close(PreciseReal, Real, size_t, const string&, const string&, int) [with PreciseReal = long double; Real = boost::math::concepts::big_real_concept; size_t = long unsigned int; std::string = std::__cxx11::basic_string<char>]'  
test/test_constants.cpp:95:4:   required from 'void test_spots(RealType) [with RealType = boost::math::concepts::big_real_concept]'  
test/test_constants.cpp:806:58:   required from here  
../../boost/math/tools/precision.hpp:72:51: error: static assertion failed: ::std::numeric_limits<T>::is_specialized  
   72 |    BOOST_STATIC_ASSERT( ::std::numeric_limits<T>::is_specialized);  
      |                                                   ^~~~~~~~~~~~~~  
../../boost/static_assert.hpp:70:55: note: in definition of macro 'BOOST_STATIC_ASSERT'  
   70 | #     define BOOST_STATIC_ASSERT( ... ) static_assert(__VA_ARGS__, #__VA_ARGS__)  
      |                                                       ^~~~~~~~~~~  
compilation terminated due to -Wfatal-errors.  
```

---

## Review 3 [Commented]

> Username: pdimov  
> Created_at: 2020-05-08 20:03:12 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/351#pullrequestreview-408491519  

📁 include/boost/math/constants/calculate_constants.hpp

```diff
1003 |+    // N is binary digits??
1004 |+    // We know the constant to 1018 decimal digits.
1005 |+    // We can't static_assert since static assert is C++11;
```

> Username: pdimov  
> Created_at: 2020-05-08 20:03:11 UTC  
> Updated_at: 2020-06-03 13:21:10 UTC  
> Url: https://github.com/boostorg/math/pull/351#discussion_r422345995  

Why not BOOST_STATIC_ASSERT?

> Username: NAThompson  
> Created_at: 2020-05-08 20:50:37 UTC  
> Updated_at: 2020-06-03 13:21:10 UTC  
> Url: https://github.com/boostorg/math/pull/351#discussion_r422367467  

@pdimov : Great suggestion, thanks!


---

## Comment 4

> Username: NAThompson  
> Created_at: 2020-05-31 18:06:45 UTC  
> Url: https://github.com/boostorg/math/pull/351#issuecomment-636506644  

@jzmaddock: Could you take a look at this at your leisure? I've removed a few old workarounds that I'd like to make sure aren't still relevant.

---

## Review 5 [Commented]

> Username: jzmaddock  
> Created_at: 2020-05-31 18:47:09 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/351#pullrequestreview-421518434  

📁 include/boost/math/concepts/real_concept.hpp

```diff
 326 | inline std::basic_istream<charT, traits>& operator>>(std::basic_istream<charT, traits>& is, real_concept& a)
 327 | {
 328 |- #if defined(BOOST_MSVC) && defined(__SGI_STL_PORT)
```

> Username: jzmaddock  
> Created_at: 2020-05-31 18:47:08 UTC  
> Updated_at: 2020-06-03 13:21:10 UTC  
> Url: https://github.com/boostorg/math/pull/351#discussion_r432973334  

Removing all these old workarounds looks sensible to me.


---

## Review 6 [Commented]

> Username: jzmaddock  
> Created_at: 2020-05-31 18:49:46 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/351#pullrequestreview-421518597  

📁 include/boost/math/constants/calculate_constants.hpp

```diff
1007 |+ 
1008 |+    BOOST_STATIC_ASSERT_MSG(N < 3.321*1018, "\nThe Feigenbaum constant cannot be computed at runtime; it is too expensive. It is known to 1018 decimal digits; you must request less than that.");
1009 |+    T alpha{"4.6692016091029906718532038204662016172581855774757686327456513430041343302113147371386897440239480138171659848551898151344086271420279325223124429888908908599449354632367134115324817142199474556443658237932020095610583305754586176522220703854106467494942849814533917262005687556659523398756038256372256480040951071283890611844702775854285419801113440175002428585382498335715522052236087250291678860362674527213399057131606875345083433934446103706309452019115876972432273589838903794946257251289097948986768334611626889116563123474460575179539122045562472807095202198199094558581946136877445617396074115614074243754435499204869180982648652368438702799649017397793425134723808737136211601860128186102056381818354097598477964173900328936171432159878240789776614391395764037760537119096932066998361984288981837003229412030210655743295550388845849737034727532121925706958414074661841981961006129640161487712944415901405467941800198133253378592493365883070459999938375411726563553016862529032210862320550634510679399023341675"};
```

> Username: jzmaddock  
> Created_at: 2020-05-31 18:49:46 UTC  
> Updated_at: 2020-06-03 13:21:10 UTC  
> Url: https://github.com/boostorg/math/pull/351#discussion_r432973507  

Does this still pass in C++03 mode?  I'm not fussed if it only generates an error in C++03 when instantiated, just don't want to break the whole header just yet.

> Username: NAThompson  
> Created_at: 2020-05-31 21:03:01 UTC  
> Updated_at: 2020-06-03 13:21:10 UTC  
> Url: https://github.com/boostorg/math/pull/351#discussion_r432983954  

Just started a CI cycle to check; the use of `BOOST_STATIC_ASSERT_MSG` was especially to keep C++03 compatibility.


---

## Comment 7

> Username: jzmaddock  
> Created_at: 2020-05-31 18:51:50 UTC  
> Url: https://github.com/boostorg/math/pull/351#issuecomment-636512418  

Other than the one minor comment about the C++11'ism in constants.hpp (can you double check that?)  this all looks fine to me, so please do go ahead and merge.

---

## Comment 8

> Username: NAThompson  
> Created_at: 2020-06-01 13:45:48 UTC  
> Url: https://github.com/boostorg/math/pull/351#issuecomment-636869817  

@jzmaddock : Are these instructions in `constants.qbk` still correct?  
  
```  
 3. It is necessary to link to the Boost.Regex library, and probably to your chosen arbitrary precision type library.  
  
4. You need to add libs\math\include_private to your compiler's include path as the needed header is not installed in the usual places by default (this avoids a cyclic dependency between the Math and Multiprecision library's headers).   
```

---
