# #22 - Boost library type_index failed to build due to c2131 c2280 [Closed]

> Username: spacelg  
> Created at: 2018-10-24 02:10:53 UTC  
> Updated at: 2018-10-26 17:50:55 UTC  
> Closed at: 2018-10-26 02:00:50 UTC  
> Url: https://github.com/boostorg/type_index/issues/22  

Environment:  
 VS 2017 + Windows Server 2016  
  
Issue description:  
 We build and run test for Boost. And we found type_index_constexpr_test.cpp failed to build due to error c2131: expression did not evaluate to a constant  
.\boost/type_index/ctti_type_index.hpp(185): note: failure was caused by attempting to access a member on an object of dynamic type 'boost::typeindex::type_index_facade<boost::typeindex::ctti_type_index,boost::typeindex::detail::ctti_data>' in which the member is not defined.  
error C2280: 'boost::typeindex::detail::ctti_data::ctti_data(const boost::typeindex::detail::ctti_data &)': attempting to reference a deleted function  
.\boost/type_index/ctti_type_index.hpp(64): note: see declaration of 'boost::typeindex::detail::ctti_data::ctti_data'  
.\boost/type_index/ctti_type_index.hpp(64): note: 'boost::typeindex::detail::ctti_data::ctti_data(const boost::typeindex::detail::ctti_data &)': function was explicitly deleted  
  
Could you pleaes take a look? Thanks in advance.  
[log_x86_test_113.log](https://github.com/boostorg/type_index/files/2508722/log_x86_test_113.log)  
  
Update repro steps:  
1. git clone -c core.autocrlf=true --recursive ​https://github.com/boostorg/boost.git D:\Boost\src  
2. open a VS 2017 x86 command prompt and browse to D:\Boost\src  
3. .\bootstrap  
4. .\b2 headers variant=release --build-dir=..\out\x86rel address-model=32  
5. .\b2 variant=release --build-dir=..\out\x86rel address-model=32  
6. .\b2 -j4 variant=release --build-dir=..\out\x86rel libs\type_index\test  
  
Error info:  
libs\type_index\test\type_index_constexpr_test.cpp(97): error C2131: expression did not evaluate to a constant  
.\boost/type_index/ctti_type_index.hpp(185): note: failure was caused by attempting to access a member on an object of dynamic type 'boost::typeindex::type_index_facade<boost::typeindex::ctti_type_index,boost::typeindex::detail::ctti_data>' in which the member is not defined  
.\boost/type_index/ctti_type_index.hpp(185): note: see usage of 'boost::typeindex::ctti_type_index::data_'  
libs\type_index\test\type_index_constexpr_test.cpp(100): error C2131: expression did not evaluate to a constant  
.\boost/type_index/ctti_type_index.hpp(185): note: failure was caused by attempting to access a member on an object of dynamic type 'boost::typeindex::type_index_facade<boost::typeindex::ctti_type_index,boost::typeindex::detail::ctti_data>' in which the member is not defined  
.\boost/type_index/ctti_type_index.hpp(185): note: see usage of 'boost::typeindex::ctti_type_index::data_'  
libs\type_index\test\type_index_constexpr_test.cpp(103): error C2131: expression did not evaluate to a constant  
.\boost/type_index/ctti_type_index.hpp(185): note: failure was caused by attempting to access a member on an object of dynamic type 'boost::typeindex::type_index_facade<boost::typeindex::ctti_type_index,boost::typeindex::detail::ctti_data>' in which the member is not defined  
.\boost/type_index/ctti_type_index.hpp(185): note: see usage of 'boost::typeindex::ctti_type_index::data_'  
libs\type_index\test\type_index_constexpr_test.cpp(106): error C2131: expression did not evaluate to a constant  
.\boost/type_index/ctti_type_index.hpp(185): note: failure was caused by attempting to access a member on an object of dynamic type 'boost::typeindex::type_index_facade<boost::typeindex::ctti_type_index,boost::typeindex::detail::ctti_data>' in which the member is not defined  
.\boost/type_index/ctti_type_index.hpp(185): note: see usage of 'boost::typeindex::ctti_type_index::data_'  
libs\type_index\test\type_index_constexpr_test.cpp(109): error C2131: expression did not evaluate to a constant  
.\boost/type_index/ctti_type_index.hpp(185): note: failure was caused by attempting to access a member on an object of dynamic type 'boost::typeindex::type_index_facade<boost::typeindex::ctti_type_index,boost::typeindex::detail::ctti_data>' in which the member is not defined  
.\boost/type_index/ctti_type_index.hpp(185): note: see usage of 'boost::typeindex::ctti_type_index::data_'  
libs\type_index\test\type_index_constexpr_test.cpp(112): error C2131: expression did not evaluate to a constant  
.\boost/type_index/ctti_type_index.hpp(185): note: failure was caused by attempting to access a member on an object of dynamic type 'boost::typeindex::type_index_facade<boost::typeindex::ctti_type_index,boost::typeindex::detail::ctti_data>' in which the member is not defined  
.\boost/type_index/ctti_type_index.hpp(185): note: see usage of 'boost::typeindex::ctti_type_index::data_'  
libs\type_index\test\type_index_constexpr_test.cpp(116): error C2131: expression did not evaluate to a constant  
.\boost/type_index/ctti_type_index.hpp(185): note: failure was caused by attempting to access a member on an object of dynamic type 'boost::typeindex::type_index_facade<boost::typeindex::ctti_type_index,boost::typeindex::detail::ctti_data>' in which the member is not defined  
.\boost/type_index/ctti_type_index.hpp(185): note: see usage of 'boost::typeindex::ctti_type_index::data_'  
libs\type_index\test\type_index_constexpr_test.cpp(119): error C2131: expression did not evaluate to a constant  
.\boost/type_index/ctti_type_index.hpp(185): note: failure was caused by attempting to access a member on an object of dynamic type 'boost::typeindex::type_index_facade<boost::typeindex::ctti_type_index,boost::typeindex::detail::ctti_data>' in which the member is not defined  
.\boost/type_index/ctti_type_index.hpp(185): note: see usage of 'boost::typeindex::ctti_type_index::data_'  
libs\type_index\test\type_index_constexpr_test.cpp(122): error C2131: expression did not evaluate to a constant  
.\boost/type_index/ctti_type_index.hpp(185): note: failure was caused by attempting to access a member on an object of dynamic type 'boost::typeindex::type_index_facade<boost::typeindex::ctti_type_index,boost::typeindex::detail::ctti_data>' in which the member is not defined  
.\boost/type_index/ctti_type_index.hpp(185): note: see usage of 'boost::typeindex::ctti_type_index::data_'  
libs\type_index\test\type_index_test_ctti_copy_fail.cpp(12): error C2280: 'boost::typeindex::detail::ctti_data::ctti_data(const boost::typeindex::detail::ctti_data &)': attempting to reference a deleted function  
.\boost/type_index/ctti_type_index.hpp(64): note: see declaration of 'boost::typeindex::detail::ctti_data::ctti_data'  
.\boost/type_index/ctti_type_index.hpp(64): note: 'boost::typeindex::detail::ctti_data::ctti_data(const boost::typeindex::detail::ctti_data &)': function was explicitly deleted

---

## Comment 1

> Username: apolukhin  
> Created at: 2018-10-24 18:57:54 UTC  
> Url: https://github.com/boostorg/type_index/issues/22#issuecomment-432785957  

This is most likely an issue in VS 2017. Other compiler pass the test well https://www.boost.org/development/tests/develop/developer/type_index.html  
  
Unfortunately I do not have access to VS compiler and can not bisect and report the issue. Could you please report it to the Visual Studio?

---

## Comment 2

> Username: spacelg  
> Created at: 2018-10-26 02:00:50 UTC  
> Url: https://github.com/boostorg/type_index/issues/22#issuecomment-433261629  

Thanks for feedback this issue, this appears to be either a compiler bug or a divergence between MSVC and the other compilers. We've reported it to Visual Studio. So close it.

---

## Comment 3

> Username: apolukhin  
> Created at: 2018-10-26 17:50:55 UTC  
> Url: https://github.com/boostorg/type_index/issues/22#issuecomment-433490750  

Could you please share the link to the report? My help could be helpful in resolving it.
