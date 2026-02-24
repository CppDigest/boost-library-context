# #848 Define C++20 concepts [Open]

> Username: mborland  
> Created at: 2022-10-16 22:14:07 UTC  
> Updated at: 2022-11-29 16:40:30 UTC  
> Url: https://github.com/boostorg/math/pull/848  

@jzmaddock, @ckormanyos, and @NAThompson I am looking for some feedback on this PR. This defines new C++20esque concepts for our use, and `abs.hpp` shows both ways to call them to preserve existing SFINAE expressions. I think this would be useful to spread around the codebase to reduce nasty template errors once matured. Thanks for your help.

---

## Review 1 [Commented]

> Username: NAThompson  
> Created_at: 2022-10-16 22:33:49 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/848#pullrequestreview-1143348650  

📁 include/boost/math/concepts/concepts.hpp

```diff
  19 |+ concept Integral = std::is_integral_v<T>
  20 |+                    #ifdef __SIZEOF_INT128__
  21 |+                    || std::is_same_v<__int128_t, T>
```

> Username: NAThompson  
> Created_at: 2022-10-16 22:33:48 UTC  
> Updated_at: 2022-10-16 22:33:49 UTC  
> Url: https://github.com/boostorg/math/pull/848#discussion_r996505099  

Would this work with `gmp` integral types?

> Username: mborland  
> Created_at: 2022-10-16 23:33:25 UTC  
> Url: https://github.com/boostorg/math/pull/848#discussion_r996525080  

Should built-ins or arbitrary precision types be the special case? I am thinking `BOOST_MATH_INTEGRAL` and `BOOST_MATH_ARBITRARY_INTEGRAL`


---

## Review 2 [Commented]

> Username: NAThompson  
> Created_at: 2022-10-16 22:34:12 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/848#pullrequestreview-1143348707  

📁 include/boost/math/concepts/concepts.hpp

```diff
  42 |+ concept Real = std::is_floating_point_v<T>
  43 |+                #ifdef BOOST_HAS_FLOAT128
  44 |+                || std::is_same_v<__float128, T>
```

> Username: NAThompson  
> Created_at: 2022-10-16 22:34:12 UTC  
> Url: https://github.com/boostorg/math/pull/848#discussion_r996505149  

Would this work with `MPFR`/ `multiprecision` types?

> Username: mborland  
> Created_at: 2022-10-16 23:34:58 UTC  
> Updated_at: 2022-10-16 23:34:59 UTC  
> Url: https://github.com/boostorg/math/pull/848#discussion_r996525315  

Same question as with integral types


---

## Comment 3

> Username: jzmaddock  
> Created_at: 2022-10-17 11:56:52 UTC  
> Url: https://github.com/boostorg/math/pull/848#issuecomment-1280740738  

Some questions:  
  
* What is the impact on compile time of using concepts rather than SFINAE or nothing at all?  
* Can we do "sophisticated" concepts that model for example the requirements that the special functions and distributions require: https://github.com/boostorg/math/blob/develop/include/boost/math/concepts/real_concept.hpp without it exploding the compiler?  
* For the constexpr-math functions the requirements are much smaller, but we require constexpr arithmetic operators rather than is_floating_point as such.  Again is this possible to check without exploding compile times?  
* How's current compiler support?  Better than modules I hope!  
  
I'm sure there are more detailed questions that will arise in time, we need to figure out what the general pro's and cons are first.

---

## Comment 4

> Username: mborland  
> Created_at: 2022-10-17 15:58:56 UTC  
> Url: https://github.com/boostorg/math/pull/848#issuecomment-1281100327  

> Some questions:  
>   
> * What is the impact on compile time of using concepts rather than SFINAE or nothing at all?  
  
Concepts [decrease compile times](https://thecppdev.com/2022/05/21/concepts-vs-sfinae-compilation-time-comparison/?noamp=available) when replacing SFINAE expressions. I expect they will increase if we are replacing `typename T` with `concept T`, but haven't found any benchmarks for that  
  
> * Can we do "sophisticated" concepts that model for example the requirements that the special functions and distributions require: https://github.com/boostorg/math/blob/develop/include/boost/math/concepts/real_concept.hpp without it exploding the compiler?  
  
I'll play around with it today. If that doesn't work I was thinking about providing specializations of Boost.Type_Traits in each of Boost.Multiprecision types for both the MP type and it's underlying (if applicable). That would hit a lot of the major types additional types, and then offer some mechanism to disable `BOOST_MATH_CONCEPTS` for user rolled types that we have no knowledge of.  
  
> * For the constexpr-math functions the requirements are much smaller, but we require constexpr arithmetic operators rather than is_floating_point as such.  Again is this possible to check without exploding compile times?  
  
I can add one of the special functions to this PR that runs on arbitrary types instead of just builtins as an example. Starting with the ccmath functions offered a simpler starting point.  
  
> * How's current compiler support?  Better than modules I hope!  
>   
All of the major compilers [support concepts](https://en.cppreference.com/w/cpp/compiler_support)  
  
> I'm sure there are more detailed questions that will arise in time, we need to figure out what the general pro's and cons are first.

---

## Comment 5

> Username: mborland  
> Created_at: 2022-10-17 20:21:58 UTC  
> Url: https://github.com/boostorg/math/pull/848#issuecomment-1281445720  

Here's a small comparison of the impacts running `../../../b2 test_beta toolset=clang cxxstd=11,14,17,20`   
  
using current develop: `60.35s user 6.04s system 543% cpu 12.204 total`  
with concepts:  `60.41s user 6.56s system 544% cpu 12.294 total`  
  
Obviously I should run this many more times to be scientific, but there is not obvious divergence between the two.

---

## Comment 6

> Username: mborland  
> Created_at: 2022-10-20 01:40:15 UTC  
> Url: https://github.com/boostorg/math/pull/848#issuecomment-1284790033  

@jzmaddock and @NAThompson I think this is good for review and comments. ccmath/abs, beta, and univariate statistics have been converted to use concepts. As usual Apple's Clang needed some help, but everything is green on CI now.

---

## Comment 7

> Username: jzmaddock  
> Created_at: 2022-11-04 17:44:28 UTC  
> Url: https://github.com/boostorg/math/pull/848#issuecomment-1303939733  

Thanks Matt, just pulling down the code to experiment with now...

---

## Comment 8

> Username: jzmaddock  
> Created_at: 2022-11-10 19:20:12 UTC  
> Url: https://github.com/boostorg/math/pull/848#issuecomment-1310785057  

OK, just messing about with this, I'm seeing a small impact on compile times, which mostly seem to be coming from parsing the new header.  I converted log1p.hpp as one of the smallest (most sensitive?) headers to try this out on, and only applied the concepts to the user-callable functions, ie not the code in the detail namespace as IMO that gains nothing from concept checks?  Then with a trivial test program:  
  
```  
#include <boost/math/special_functions/log1p.hpp>  
#include <iostream>  
  
int main()  
{  
   using Real = double;  
   Real d = boost::math::log1p(Real(2));  
  
   return 0;  
}  
```  
  
Build times with -O3 -std=c++20 and cygwin gcc were 4.4s with -DBOOST_MATH_DISABLE_CONCEPTS rising to 5s without the define.  Including the new concepts.hpp header but without using them, saw similar compile times to where they were used, so at present this looks like a fixed cost of parsing the header.

---

## Comment 9

> Username: jzmaddock  
> Created_at: 2022-11-10 19:27:56 UTC  
> Url: https://github.com/boostorg/math/pull/848#issuecomment-1310794327  

MSVC build times for the same example are a bit variable, but it looks like around 3s with -DBOOST_MATH_DISABLE_CONCEPTS rising to 4s without.

---

## Comment 10

> Username: jzmaddock  
> Created_at: 2022-11-10 19:39:28 UTC  
> Url: https://github.com/boostorg/math/pull/848#issuecomment-1310807091  

Confirmed that error messages are much improved though: msvc is quite terse:  
  
```  
D:\data\boost\boost\t.cpp(12,26): error C2672: 'boost::math::log1p': no matching overloaded function found  
D:\data\boost\boost\t.cpp(12,33): error C7602: 'boost::math::log1p': the associated constraints are not satisfied  
```  
  
While gcc gets a bit more verbose:  
  
```  
t.cpp: In function ‘int main()’:  
t.cpp:12:31: error: no matching function for call to ‘log1p(std::string&)’  
   12 |    Real d = boost::math::log1p(s);  
      |             ~~~~~~~~~~~~~~~~~~^~~  
In file included from t.cpp:4:  
./boost/math/special_functions/log1p.hpp:429:46: note: candidate: ‘template<class T>  requires  arbitrary_real_type<T> typename boost::math::tools::promote_args<T>::type boost::math::log1p(T)’  
  429 | inline typename tools::promote_args<T>::type log1p(T x)  
      |                                              ^~~~~  
./boost/math/special_functions/log1p.hpp:429:46: note:   template argument deduction/substitution failed:  
./boost/math/special_functions/log1p.hpp:429:46: note: constraints not satisfied  
In file included from ./boost/math/special_functions/math_fwd.hpp:32,  
                 from ./boost/math/special_functions/log1p.hpp:23,  
                 from t.cpp:4:  
t.cpp: In substitution of ‘template<class T>  requires  arbitrary_real_type<T> typename boost::math::tools::promote_args<T>::type boost::math::log1p(T) [with T = std::basic_string<char>]’:  
t.cpp:12:31:   required from here  
./boost/math/concepts/concepts.hpp:143:9:   required for the satisfaction of ‘arbitrary_arithmetic_type<T>’ [with T = std::basic_string<char, std::char_traits<char>, std::allocator<char> >]  
./boost/math/concepts/concepts.hpp:159:9:   required for the satisfaction of ‘arbitrary_real_type<T>’ [with T = std::basic_string<char, std::char_traits<char>, std::allocator<char> >]  
./boost/math/concepts/concepts.hpp:143:75: note: no operand of the disjunction is satisfied  
  143 | concept arbitrary_arithmetic_type = arbitrary_unsigned_arithmetic_type<T> ||  
      |                                     ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~^~  
  144 |                                     arbitrary_signed_arithmetic_type<T>;  
      |                                     ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
cc1plus: note: set ‘-fconcepts-diagnostics-depth=’ to at least 2 for more detail  
In file included from t.cpp:4:  
./boost/math/special_functions/log1p.hpp:298:46: note: candidate: ‘template<class T, class Policy>  requires (arbitrary_real_type<T>) && (policy<Policy>) typename boost::math::tools::promote_args<T>::type boost::math::log1p(T, const Policy&)’  
  298 | inline typename tools::promote_args<T>::type log1p(T x, const Policy&)  
      |                                              ^~~~~  
./boost/math/special_functions/log1p.hpp:298:46: note:   template argument deduction/substitution failed:  
t.cpp:12:31: note:   candidate expects 2 arguments, 1 provided  
   12 |    Real d = boost::math::log1p(s);  
      |             ~~~~~~~~~~~~~~~~~~^~~  
In file included from t.cpp:4:  
./boost/math/special_functions/log1p.hpp:332:14: note: candidate: ‘template<class Policy>  requires  policy<Policy> float boost::math::log1p(float, const Policy&)’  
  332 | inline float log1p(float x, const Policy& pol)  
      |              ^~~~~  
./boost/math/special_functions/log1p.hpp:332:14: note:   template argument deduction/substitution failed:  
t.cpp:12:31: note:   candidate expects 2 arguments, 1 provided  
   12 |    Real d = boost::math::log1p(s);  
      |             ~~~~~~~~~~~~~~~~~~^~~  
In file included from t.cpp:4:  
./boost/math/special_functions/log1p.hpp:344:20: note: candidate: ‘template<class Policy>  requires  policy<Policy> long double boost::math::log1p(long double, const Policy&)’  
  344 | inline long double log1p(long double x, const Policy& pol)  
      |                    ^~~~~  
./boost/math/special_functions/log1p.hpp:344:20: note:   template argument deduction/substitution failed:  
t.cpp:12:31: note:   candidate expects 2 arguments, 1 provided  
   12 |    Real d = boost::math::log1p(s);  
      |             ~~~~~~~~~~~~~~~~~~^~~  
./boost/math/special_functions/log1p.hpp:369:15: note: candidate: ‘template<class Policy>  requires  policy<Policy> double boost::math::log1p(double, const Policy&)’  
  369 | inline double log1p(double x, const Policy& pol)  
      |               ^~~~~  
./boost/math/special_functions/log1p.hpp:369:15: note:   template argument deduction/substitution failed:  
t.cpp:12:31: note:   candidate expects 2 arguments, 1 provided  
   12 |    Real d = boost::math::log1p(s);  
      |             ~~~~~~~~~~~~~~~~~~^~~  
```

---

## Comment 11

> Username: mborland  
> Created_at: 2022-11-11 04:07:40 UTC  
> Url: https://github.com/boostorg/math/pull/848#issuecomment-1311204619  

@jzmaddock I would concur that we probably only need to implement concepts in the user facing functions and not in the detail namespace unless it will save us compile time by replacing SFINAE with concepts.  
  
I think it is worth pointing out for posterity that passing a `std::string` to the current implementation of `boost::math::log1p` like in your example results in C++ standard template spaghetti:  
  
```  
garbage.cpp: In function 'int main()':  
garbage.cpp:8:38: error: no matching function for call to 'std::__cxx11::basic_string<char>::basic_string(int)'  
    8 |    Real d = boost::math::log1p(Real(2));  
      |                                      ^  
In file included from /opt/homebrew/Cellar/gcc/12.2.0/include/c++/12/string:53,  
                 from /opt/homebrew/Cellar/gcc/12.2.0/include/c++/12/bits/locale_classes.h:40,  
                 from /opt/homebrew/Cellar/gcc/12.2.0/include/c++/12/bits/ios_base.h:41,  
                 from /opt/homebrew/Cellar/gcc/12.2.0/include/c++/12/streambuf:41,  
                 from /opt/homebrew/Cellar/gcc/12.2.0/include/c++/12/bits/streambuf_iterator.h:35,  
                 from /opt/homebrew/Cellar/gcc/12.2.0/include/c++/12/iterator:66,  
                 from ../../../boost/iterator/iterator_traits.hpp:10,  
                 from ../../../boost/range/iterator_range_core.hpp:26,  
                 from ../../../boost/lexical_cast.hpp:30,  
                 from ../../../boost/math/tools/big_constant.hpp:12,  
                 from ../../../boost/math/special_functions/log1p.hpp:21,  
                 from garbage.cpp:1:  
/opt/homebrew/Cellar/gcc/12.2.0/include/c++/12/bits/basic_string.h:786:9: note: candidate: 'template<class _Tp, class> constexpr std::__cxx11::basic_string<_CharT, _Traits, _Alloc>::basic_string(const _Tp&, const _Alloc&) [with <template-parameter-2-2> = _Tp; _CharT = char; _Traits = std::char_traits<char>; _Alloc = std::allocator<char>]'  
  786 |         basic_string(const _Tp& __t, const _Alloc& __a = _Alloc())  
      |         ^~~~~~~~~~~~  
/opt/homebrew/Cellar/gcc/12.2.0/include/c++/12/bits/basic_string.h:786:9: note:   template argument deduction/substitution failed:  
In file included from /opt/homebrew/Cellar/gcc/12.2.0/include/c++/12/bits/stl_pair.h:60,  
                 from /opt/homebrew/Cellar/gcc/12.2.0/include/c++/12/bits/stl_algobase.h:64,  
                 from /opt/homebrew/Cellar/gcc/12.2.0/include/c++/12/bits/specfun.h:45,  
                 from /opt/homebrew/Cellar/gcc/12.2.0/include/c++/12/cmath:1935,  
                 from ../../../boost/math/special_functions/log1p.hpp:15:  
/opt/homebrew/Cellar/gcc/12.2.0/include/c++/12/type_traits: In substitution of 'template<bool _Cond, class _Tp> using enable_if_t = typename std::enable_if::type [with bool _Cond = false; _Tp = void]':  
/opt/homebrew/Cellar/gcc/12.2.0/include/c++/12/bits/basic_string.h:155:8:   required by substitution of 'template<class _CharT, class _Traits, class _Alloc> template<class _Tp, class _Res> using _If_sv = std::enable_if_t<std::__and_<std::is_convertible<const _Tp&, std::basic_string_view<_CharT, _Traits> >, std::__not_<std::is_convertible<const _Tp*, const std::__cxx11::basic_string<_CharT, _Traits, _Alloc>*> >, std::__not_<std::is_convertible<const _Tp&, const _CharT*> > >::value, _Res> [with _Tp = int; _Res = void; _CharT = char; _Traits = std::char_traits<char>; _Alloc = std::allocator<char>]'  
/opt/homebrew/Cellar/gcc/12.2.0/include/c++/12/bits/basic_string.h:783:30:   required from here  
/opt/homebrew/Cellar/gcc/12.2.0/include/c++/12/type_traits:2614:11: error: no type named 'type' in 'struct std::enable_if<false, void>'  
 2614 |     using enable_if_t = typename enable_if<_Cond, _Tp>::type;  
      |           ^~~~~~~~~~~  
/opt/homebrew/Cellar/gcc/12.2.0/include/c++/12/bits/basic_string.h:774:9: note: candidate: 'template<class _Tp, class> constexpr std::__cxx11::basic_string<_CharT, _Traits, _Alloc>::basic_string(const _Tp&, size_type, size_type, const _Alloc&) [with <template-parameter-2-2> = _Tp; _CharT = char; _Traits = std::char_traits<char>; _Alloc = std::allocator<char>]'  
  774 |         basic_string(const _Tp& __t, size_type __pos, size_type __n,  
      |         ^~~~~~~~~~~~  
/opt/homebrew/Cellar/gcc/12.2.0/include/c++/12/bits/basic_string.h:774:9: note:   template argument deduction/substitution failed:  
garbage.cpp:8:38: note:   candidate expects 4 arguments, 1 provided  
    8 |    Real d = boost::math::log1p(Real(2));  
      |                                      ^  
/opt/homebrew/Cellar/gcc/12.2.0/include/c++/12/bits/basic_string.h:751:9: note: candidate: 'template<class _InputIterator, class> constexpr std::__cxx11::basic_string<_CharT, _Traits, _Alloc>::basic_string(_InputIterator, _InputIterator, const _Alloc&) [with <template-parameter-2-2> = _InputIterator; _CharT = char; _Traits = std::char_traits<char>; _Alloc = std::allocator<char>]'  
  751 |         basic_string(_InputIterator __beg, _InputIterator __end,  
      |         ^~~~~~~~~~~~  
/opt/homebrew/Cellar/gcc/12.2.0/include/c++/12/bits/basic_string.h:751:9: note:   template argument deduction/substitution failed:  
garbage.cpp:8:38: note:   candidate expects 3 arguments, 1 provided  
    8 |    Real d = boost::math::log1p(Real(2));  
      |                                      ^  
/opt/homebrew/Cellar/gcc/12.2.0/include/c++/12/bits/basic_string.h:657:7: note: candidate: 'template<class> constexpr std::__cxx11::basic_string<_CharT, _Traits, _Alloc>::basic_string(size_type, _CharT, const _Alloc&) [with _CharT = char; _Traits = std::char_traits<char>; _Alloc = std::allocator<char>]'  
  657 |       basic_string(size_type __n, _CharT __c, const _Alloc& __a = _Alloc())  
      |       ^~~~~~~~~~~~  
/opt/homebrew/Cellar/gcc/12.2.0/include/c++/12/bits/basic_string.h:657:7: note:   template argument deduction/substitution failed:  
garbage.cpp:8:38: note:   candidate expects 3 arguments, 1 provided  
    8 |    Real d = boost::math::log1p(Real(2));  
      |                                      ^  
/opt/homebrew/Cellar/gcc/12.2.0/include/c++/12/bits/basic_string.h:634:7: note: candidate: 'template<class> constexpr std::__cxx11::basic_string<_CharT, _Traits, _Alloc>::basic_string(const _CharT*, const _Alloc&) [with _CharT = char; _Traits = std::char_traits<char>; _Alloc = std::allocator<char>]'  
  634 |       basic_string(const _CharT* __s, const _Alloc& __a = _Alloc())  
      |       ^~~~~~~~~~~~  
/opt/homebrew/Cellar/gcc/12.2.0/include/c++/12/bits/basic_string.h:634:7: note:   template argument deduction/substitution failed:  
garbage.cpp:8:37: note:   cannot convert '2' (type 'int') to type 'const char*'  
    8 |    Real d = boost::math::log1p(Real(2));  
      |                                     ^  
/opt/homebrew/Cellar/gcc/12.2.0/include/c++/12/bits/basic_string.h:708:7: note: candidate: 'constexpr std::__cxx11::basic_string<_CharT, _Traits, _Alloc>::basic_string(std::__cxx11::basic_string<_CharT, _Traits, _Alloc>&&, const _Alloc&) [with _CharT = char; _Traits = std::char_traits<char>; _Alloc = std::allocator<char>]'  
  708 |       basic_string(basic_string&& __str, const _Alloc& __a)  
      |       ^~~~~~~~~~~~  
/opt/homebrew/Cellar/gcc/12.2.0/include/c++/12/bits/basic_string.h:708:7: note:   candidate expects 2 arguments, 1 provided  
/opt/homebrew/Cellar/gcc/12.2.0/include/c++/12/bits/basic_string.h:703:7: note: candidate: 'constexpr std::__cxx11::basic_string<_CharT, _Traits, _Alloc>::basic_string(const std::__cxx11::basic_string<_CharT, _Traits, _Alloc>&, const _Alloc&) [with _CharT = char; _Traits = std::char_traits<char>; _Alloc = std::allocator<char>]'  
  703 |       basic_string(const basic_string& __str, const _Alloc& __a)  
      |       ^~~~~~~~~~~~  
/opt/homebrew/Cellar/gcc/12.2.0/include/c++/12/bits/basic_string.h:703:7: note:   candidate expects 2 arguments, 1 provided  
/opt/homebrew/Cellar/gcc/12.2.0/include/c++/12/bits/basic_string.h:698:7: note: candidate: 'constexpr std::__cxx11::basic_string<_CharT, _Traits, _Alloc>::basic_string(std::initializer_list<_Tp>, const _Alloc&) [with _CharT = char; _Traits = std::char_traits<char>; _Alloc = std::allocator<char>]'  
  698 |       basic_string(initializer_list<_CharT> __l, const _Alloc& __a = _Alloc())  
      |       ^~~~~~~~~~~~  
/opt/homebrew/Cellar/gcc/12.2.0/include/c++/12/bits/basic_string.h:698:45: note:   no known conversion for argument 1 from 'int' to 'std::initializer_list<char>'  
  698 |       basic_string(initializer_list<_CharT> __l, const _Alloc& __a = _Alloc())  
      |                    ~~~~~~~~~~~~~~~~~~~~~~~~~^~~  
/opt/homebrew/Cellar/gcc/12.2.0/include/c++/12/bits/basic_string.h:670:7: note: candidate: 'constexpr std::__cxx11::basic_string<_CharT, _Traits, _Alloc>::basic_string(std::__cxx11::basic_string<_CharT, _Traits, _Alloc>&&) [with _CharT = char; _Traits = std::char_traits<char>; _Alloc = std::allocator<char>]'  
  670 |       basic_string(basic_string&& __str) noexcept  
      |       ^~~~~~~~~~~~  
/opt/homebrew/Cellar/gcc/12.2.0/include/c++/12/bits/basic_string.h:670:35: note:   no known conversion for argument 1 from 'int' to 'std::__cxx11::basic_string<char>&&'  
  670 |       basic_string(basic_string&& __str) noexcept  
      |                    ~~~~~~~~~~~~~~~^~~~~  
/opt/homebrew/Cellar/gcc/12.2.0/include/c++/12/bits/basic_string.h:612:7: note: candidate: 'constexpr std::__cxx11::basic_string<_CharT, _Traits, _Alloc>::basic_string(const _CharT*, size_type, const _Alloc&) [with _CharT = char; _Traits = std::char_traits<char>; _Alloc = std::allocator<char>; size_type = long unsigned int]'  
  612 |       basic_string(const _CharT* __s, size_type __n,  
      |       ^~~~~~~~~~~~  
/opt/homebrew/Cellar/gcc/12.2.0/include/c++/12/bits/basic_string.h:612:7: note:   candidate expects 3 arguments, 1 provided  
/opt/homebrew/Cellar/gcc/12.2.0/include/c++/12/bits/basic_string.h:592:7: note: candidate: 'constexpr std::__cxx11::basic_string<_CharT, _Traits, _Alloc>::basic_string(const std::__cxx11::basic_string<_CharT, _Traits, _Alloc>&, size_type, size_type, const _Alloc&) [with _CharT = char; _Traits = std::char_traits<char>; _Alloc = std::allocator<char>; size_type = long unsigned int]'  
  592 |       basic_string(const basic_string& __str, size_type __pos,  
      |       ^~~~~~~~~~~~  
/opt/homebrew/Cellar/gcc/12.2.0/include/c++/12/bits/basic_string.h:592:7: note:   candidate expects 4 arguments, 1 provided  
/opt/homebrew/Cellar/gcc/12.2.0/include/c++/12/bits/basic_string.h:574:7: note: candidate: 'constexpr std::__cxx11::basic_string<_CharT, _Traits, _Alloc>::basic_string(const std::__cxx11::basic_string<_CharT, _Traits, _Alloc>&, size_type, size_type) [with _CharT = char; _Traits = std::char_traits<char>; _Alloc = std::allocator<char>; size_type = long unsigned int]'  
  574 |       basic_string(const basic_string& __str, size_type __pos,  
      |       ^~~~~~~~~~~~  
/opt/homebrew/Cellar/gcc/12.2.0/include/c++/12/bits/basic_string.h:574:7: note:   candidate expects 3 arguments, 1 provided  
/opt/homebrew/Cellar/gcc/12.2.0/include/c++/12/bits/basic_string.h:557:7: note: candidate: 'constexpr std::__cxx11::basic_string<_CharT, _Traits, _Alloc>::basic_string(const std::__cxx11::basic_string<_CharT, _Traits, _Alloc>&, size_type, const _Alloc&) [with _CharT = char; _Traits = std::char_traits<char>; _Alloc = std::allocator<char>; size_type = long unsigned int]'  
  557 |       basic_string(const basic_string& __str, size_type __pos,  
      |       ^~~~~~~~~~~~  
/opt/homebrew/Cellar/gcc/12.2.0/include/c++/12/bits/basic_string.h:557:7: note:   candidate expects 3 arguments, 1 provided  
/opt/homebrew/Cellar/gcc/12.2.0/include/c++/12/bits/basic_string.h:540:7: note: candidate: 'constexpr std::__cxx11::basic_string<_CharT, _Traits, _Alloc>::basic_string(const std::__cxx11::basic_string<_CharT, _Traits, _Alloc>&) [with _CharT = char; _Traits = std::char_traits<char>; _Alloc = std::allocator<char>]'  
  540 |       basic_string(const basic_string& __str)  
      |       ^~~~~~~~~~~~  
/opt/homebrew/Cellar/gcc/12.2.0/include/c++/12/bits/basic_string.h:540:40: note:   no known conversion for argument 1 from 'int' to 'const std::__cxx11::basic_string<char>&'  
  540 |       basic_string(const basic_string& __str)  
      |                    ~~~~~~~~~~~~~~~~~~~~^~~~~  
/opt/homebrew/Cellar/gcc/12.2.0/include/c++/12/bits/basic_string.h:528:7: note: candidate: 'constexpr std::__cxx11::basic_string<_CharT, _Traits, _Alloc>::basic_string(const _Alloc&) [with _CharT = char; _Traits = std::char_traits<char>; _Alloc = std::allocator<char>]'  
  528 |       basic_string(const _Alloc& __a) _GLIBCXX_NOEXCEPT  
      |       ^~~~~~~~~~~~  
/opt/homebrew/Cellar/gcc/12.2.0/include/c++/12/bits/basic_string.h:528:34: note:   no known conversion for argument 1 from 'int' to 'const std::allocator<char>&'  
  528 |       basic_string(const _Alloc& __a) _GLIBCXX_NOEXCEPT  
      |                    ~~~~~~~~~~~~~~^~~  
/opt/homebrew/Cellar/gcc/12.2.0/include/c++/12/bits/basic_string.h:515:7: note: candidate: 'constexpr std::__cxx11::basic_string<_CharT, _Traits, _Alloc>::basic_string() [with _CharT = char; _Traits = std::char_traits<char>; _Alloc = std::allocator<char>]'  
  515 |       basic_string()  
      |       ^~~~~~~~~~~~  
/opt/homebrew/Cellar/gcc/12.2.0/include/c++/12/bits/basic_string.h:515:7: note:   candidate expects 0 arguments, 1 provided  
/opt/homebrew/Cellar/gcc/12.2.0/include/c++/12/bits/basic_string.h:187:7: note: candidate: 'constexpr std::__cxx11::basic_string<_CharT, _Traits, _Alloc>::basic_string(__sv_wrapper, const _Alloc&) [with _CharT = char; _Traits = std::char_traits<char>; _Alloc = std::allocator<char>]'  
  187 |       basic_string(__sv_wrapper __svw, const _Alloc& __a)  
      |       ^~~~~~~~~~~~  
/opt/homebrew/Cellar/gcc/12.2.0/include/c++/12/bits/basic_string.h:187:7: note:   candidate expects 2 arguments, 1 provided  
  
    "g++-12"   -std=c++20 -fvisibility-inlines-hidden -O0 -fno-inline -Wall -g -fvisibility=hidden  -DBOOST_ALL_NO_LIB=1 -DBOOST_TEST_NO_AUTO_LINK=1 -DBOOST_UBLAS_UNSUPPORTED_COMPILER=0  -I"." -I"../../.." -I"../include_private"  -c -o "../../../bin.v2/libs/math/test/garbage.test/gcc-12/debug/cxxstd-20-iso/link-static/threading-multi/visibility-hidden/garbage.o" "garbage.cpp"  
```  
  
Do you think this is worth continuing to pursue? I think that a slight increase in compile time is worth it to make our error messages significantly more palatable to the user.

---

## Comment 12

> Username: mborland  
> Created_at: 2022-11-29 16:40:26 UTC  
> Url: https://github.com/boostorg/math/pull/848#issuecomment-1330937525  

@jzmaddock How do you want to proceed on this one? I grabbed the changes to log1p off your branch, and CI is green with all of the changes so far. Since the winter development cycle is much longer than the others I think it would be worth merging something close to see what the downstream side effects are on a limited number of functions.

---
