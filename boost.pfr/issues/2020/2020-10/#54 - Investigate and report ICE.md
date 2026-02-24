# #54 - Investigate and report ICE [Open]

> Username: apolukhin  
> Created at: 2020-10-14 15:09:49 UTC  
> Updated at: 2020-12-16 14:47:36 UTC  
> Url: https://github.com/boostorg/pfr/issues/54  

> *test-2 and test-3:*  
> https://github.com/madmongo1/pfr_review/blob/master/pre-cxx20/test-2.cpp  
> Seems to produce infinite recursive template expansion  
  
The example should not compile, because the nested types are not aggregates.  
  
In C++14 it gives a correct static assert  
  
../../../boost/pfr/detail/core14_classic.hpp:509:5: error:  
static_assert failed "====================> Boost.PFR: Type can not be  
used is flat_ functions, because it's not POD"  
  
in C++17 it also gives a correct static assert  
  
../../../boost/pfr/detail/fields_count.hpp:225:9: error: static  
assertion failed: ====================> Boost.PFR: Type must be  
aggregate initializable.  
  
  
Clang crashes after that.  
GCC goes into an infinite loop, ignoring all the template  
instantiation depth limitations and static_assert messages printing.  
  
  
Using BOOST_PFR_PRECISE_FUNCTIONS_FOR now gives a complaint of missing  
sd::hash specialization:  
  
../../../boost/pfr/detail/functional.hpp:133:9: error: static_assert  
failed due to requirement 'sizeof(boost::basic_string_view<char,  
std::char_traits<char> >) && false' "====================> Boost.PFR:  
std::hash not specialized for type T"  
  
I've added the tests and will report issues to the compiler developers soon.

---

## Comment 1

> Username: apolukhin  
> Created at: 2020-12-16 14:47:35 UTC  
> Url: https://github.com/boostorg/pfr/issues/54#issuecomment-746408754  

Clang crush was fixes somewhere between clang-[10, 12]  
  
GCC-9 issue could be reproduced via  
`boost/libs/pfr/test$ ../../../b2 -j3 -a toolset=gcc-9 cxxstd=14 motivating_example0_classic`
