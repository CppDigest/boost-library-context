# #256 - MSVC 1916 does not have a good constexpr 14 support [Open]

> Username: apolukhin  
> Created at: 2018-12-16 17:11:51 UTC  
> Updated at: 2019-04-02 18:42:52 UTC  
> Url: https://github.com/boostorg/config/issues/256  

Looks like `BOOST_CXX14_CONSTEXPR` should not expand into `constexpr` for _MSC_VER == 1916, because the support does not seem complete. Here are some errors from the Boost.TypeIndex tests:  
```  
[00:03:17] compile-c-c++ ..\..\..\bin.v2\libs\type_index\test\type_index_constexpr_test.test\msvc-14.1\debug\cxxstd-14-iso\threading-multi\type_index_constexpr_test.obj  
[00:03:17] type_index_constexpr_test.cpp  
[00:03:17] type_index_constexpr_test.cpp(97): error C2131: expression did not evaluate to a constant  
[00:03:17] C:\boost-local\boost/type_index/ctti_type_index.hpp(185): note: failure was caused by attempting to access a member on an object of dynamic type 'boost::typeindex::type_index_facade<boost::typeindex::ctti_type_index,boost::typeindex::detail::ctti_data>' in which the member is not defined  
[00:03:17] C:\boost-local\boost/type_index/ctti_type_index.hpp(185): note: see usage of 'boost::typeindex::ctti_type_index::data_'  
[00:03:17] type_index_constexpr_test.cpp(100): error C2131: expression did not evaluate to a constant  
```  
  
Full log is available at https://ci.appveyor.com/project/apolukhin/type-index/builds/21035855/job/9hcff3l2j4r9o70v  
  
Misc: TypeIndex workaround that helped to fix the test https://github.com/boostorg/type_index/commit/c585d7466969387402114650b0387afd428852ce .

---

## Comment 1

> Username: jzmaddock  
> Created at: 2019-04-02 18:42:51 UTC  
> Url: https://github.com/boostorg/config/issues/256#issuecomment-479141521  

Sorry for the delay in getting around to this!  
  
This is a weird one, and I'm not completely convinced msvc is at fault yet.  If I change the == operator to:  
  
```  
template <class Derived, class TypeInfo>  
BOOST_CXX14_CONSTEXPR inline bool operator == (const type_index_facade<Derived, TypeInfo>& lhs, const type_index_facade<Derived, TypeInfo>& rhs) BOOST_NOEXCEPT {  
   constexpr Derived const& d1 = static_cast<Derived const&>(lhs);  
   constexpr Derived const& d2 = static_cast<Derived const&>(rhs);  
   return d1.equal(d2);  
}  
```  
  
Then obviously it can only be used in a constexpr context, but even then both gcc and clang issue (completely inscrutable) errors.  But work just fine if I remove the `constexpr` from d1 and d2.  
  
Confused yours...
