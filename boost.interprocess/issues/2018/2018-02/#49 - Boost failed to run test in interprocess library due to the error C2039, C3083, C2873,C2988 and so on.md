# #49 - Boost failed to run test in interprocess library due to the error C2039, C3083, C2873,C2988 and so on [Closed]

> Username: shanshan0309  
> Created at: 2018-02-28 03:00:53 UTC  
> Updated at: 2018-03-02 10:47:15 UTC  
> Closed at: 2018-03-02 10:47:15 UTC  
> Url: https://github.com/boostorg/interprocess/issues/49  

We tried to build and run interprocess test for Boost. It failed to build due to the error C2039, C3083, C2873,C2988 and so on. Could you please help take a look at this? Thanks!  
  
**Reproduce steps:**  
1. git clone -c core.autocrlf=true --recursive https://github.com/boostorg/boost.git D:\Boost\src  
2. Open a VS 2015 x86 command prompt and browse to D:\Boost\src  
3. .\bootstrap  
4. .\b2 headers variant=release --build-dir=..\out\Release --address-model=32  
5. .\b2 variant=release --build-dir=..\out\Release --address-model=32  
6. .\b2 -j4 variant=release --build-dir=..\out\x86rel libs\interprocess\test  
  
**Expected result:**   
All tests passed  
  
**Actual result:**  
.\boost/interprocess/detail/variadic_templates_tools.hpp(28): error C2039: 'dtl': is not a member of 'boost::container'  
.\boost/container/detail/variadic_templates_tools.hpp(30): note: see declaration of 'boost::container'  
.\boost/interprocess/detail/variadic_templates_tools.hpp(28): error C3083: 'dtl': the symbol to the left of a '::' must be a type  
.\boost/interprocess/detail/variadic_templates_tools.hpp(28): error C2039: 'tuple': is not a member of 'boost::container'  
.\boost/container/detail/variadic_templates_tools.hpp(30): note: see declaration of 'boost::container'  
.\boost/interprocess/detail/variadic_templates_tools.hpp(28): error C2873: 'tuple': symbol cannot be used in a using-declaration  
.\boost/interprocess/detail/variadic_templates_tools.hpp(29): error C2039: 'dtl': is not a member of 'boost::container'  
.\boost/container/detail/variadic_templates_tools.hpp(30): note: see declaration of 'boost::container'  
.\boost/interprocess/detail/variadic_templates_tools.hpp(29): error C3083: 'dtl': the symbol to the left of a '::' must be a type  
.\boost/interprocess/detail/variadic_templates_tools.hpp(29): error C2039: 'build_number_seq': is not a member of 'boost::container'  
.\boost/container/detail/variadic_templates_tools.hpp(30): note: see declaration of 'boost::container'  
.\boost/interprocess/detail/variadic_templates_tools.hpp(29): error C2873: 'build_number_seq': symbol cannot be used in a using-declaration  
.\boost/interprocess/detail/variadic_templates_tools.hpp(30): error C2039: 'dtl': is not a member of 'boost::container'  
.\boost/container/detail/variadic_templates_tools.hpp(30): note: see declaration of 'boost::container'  
.\boost/interprocess/detail/variadic_templates_tools.hpp(30): error C3083: 'dtl': the symbol to the left of a '::' must be a type  
.\boost/interprocess/detail/variadic_templates_tools.hpp(30): error C2039: 'index_tuple': is not a member of 'boost::container'  
.\boost/container/detail/variadic_templates_tools.hpp(30): note: see declaration of 'boost::container'  
.\boost/interprocess/detail/variadic_templates_tools.hpp(30): error C2873: 'index_tuple': symbol cannot be used in a using-declaration  
.\boost/interprocess/detail/variadic_templates_tools.hpp(31): error C2039: 'dtl': is not a member of 'boost::container'  
.\boost/container/detail/variadic_templates_tools.hpp(30): note: see declaration of 'boost::container'  
.\boost/interprocess/detail/variadic_templates_tools.hpp(31): error C3083: 'dtl': the symbol to the left of a '::' must be a type  
.\boost/interprocess/detail/variadic_templates_tools.hpp(31): error C2039: 'get': is not a member of 'boost::container'  
.\boost/container/detail/variadic_templates_tools.hpp(30): note: see declaration of 'boost::container'  
.\boost/interprocess/detail/variadic_templates_tools.hpp(31): error C2873: 'get': symbol cannot be used in a using-declaration  
.\boost/interprocess/detail/named_proxy.hpp(53): error C2143: syntax error: missing ';' before '<'  
.\boost/interprocess/detail/named_proxy.hpp(102): note: see reference to class template instantiation 'boost::interprocess::ipcdetail::CtorArgN<T,is_iterator,Args...>' being compiled  
.\boost/interprocess/detail/named_proxy.hpp(53): error C4430: missing type specifier - int assumed. Note: C++ does not support default-int  
.\boost/interprocess/detail/named_proxy.hpp(53): error C2238: unexpected token(s) preceding ';'  
.\boost/interprocess/detail/named_proxy.hpp(80): error C4430: missing type specifier - int assumed. Note: C++ does not support default-int  
.\boost/interprocess/detail/named_proxy.hpp(80): error C2988: unrecognizable template declaration/definition  
.\boost/interprocess/detail/named_proxy.hpp(80): error C2143: syntax error: missing ',' before '<'  
.\boost/interprocess/detail/named_proxy.hpp(84): error C4430: missing type specifier - int assumed. Note: C++ does not support default-int  
.\boost/interprocess/detail/named_proxy.hpp(84): error C2988: unrecognizable template declaration/definition  
.\boost/interprocess/detail/named_proxy.hpp(84): error C2143: syntax error: missing ',' before '<'  
.\boost/interprocess/detail/named_proxy.hpp(88): error C4430: missing type specifier - int assumed. Note: C++ does not support default-int  
.\boost/interprocess/detail/named_proxy.hpp(88): error C2988: unrecognizable template declaration/definition  
.\boost/interprocess/detail/named_proxy.hpp(88): error C2143: syntax error: missing ',' before '<'  
.\boost/interprocess/detail/named_proxy.hpp(98): error C4430: missing type specifier - int assumed. Note: C++ does not support default-int  
.\boost/interprocess/detail/named_proxy.hpp(98): error C2988: unrecognizable template declaration/definition  
.\boost/interprocess/detail/named_proxy.hpp(98): error C2143: syntax error: missing ',' before '<'  
.\boost/interprocess/detail/named_proxy.hpp(101): error C2143: syntax error: missing ';' before '<'  
.\boost/interprocess/detail/named_proxy.hpp(101): error C4430: missing type specifier - int assumed. Note: C++ does not support default-int  
.\boost/interprocess/detail/named_proxy.hpp(101): error C2238: unexpected token(s) preceding ';'  
.\boost/interprocess/offset_ptr.hpp(69): error C3083: 'dtl': the symbol to the left of a '::' must be a type  
.\boost/interprocess/offset_ptr.hpp(73): note: see reference to class template instantiation 'boost::interprocess::ipcdetail::offset_ptr_internal<OffsetType,OffsetAlignment>' being compiled  
.\boost/interprocess/offset_ptr.hpp(70): error C2143: syntax error: missing ';' before '<'  
.\boost/interprocess/offset_ptr.hpp(70): error C4430: missing type specifier - int assumed. Note: C++ does not support default-int  
.\boost/interprocess/offset_ptr.hpp(71): error C2334: unexpected token(s) preceding ':'; skipping apparent function body  
.\boost/interprocess/offset_ptr.hpp(55): fatal error C1075: '{': no matching token found

---

## Comment 1

> Username: shanshan0309  
> Created at: 2018-03-01 03:19:34 UTC  
> Url: https://github.com/boostorg/interprocess/issues/49#issuecomment-369461707  

Hi, Any update for this?

---

## Comment 2

> Username: igaztanaga  
> Created at: 2018-03-01 17:24:55 UTC  
> Url: https://github.com/boostorg/interprocess/issues/49#issuecomment-369665489  

I've compiled for Visual 2015 without problems. Regression tests in "develop":  
  
http://www.boost.org/development/tests/develop/developer/interprocess.html  
  
seems fine for msvc 14.0  
  
Locally I can compile it in both develop and master branches without problems. Looking the boost repo in the master branch I can see that class in boost::container:: in the "dtl" namespace. Could try to update all libraries again and rerun those tests?

---

## Comment 3

> Username: shanshan0309  
> Created at: 2018-03-02 01:06:36 UTC  
> Url: https://github.com/boostorg/interprocess/issues/49#issuecomment-369785755  

@igaztanaga, Thanks for looking into this issue. I updated all libraries and ran successfully.

---

## Comment 4

> Username: igaztanaga  
> Created at: 2018-03-02 10:47:15 UTC  
> Url: https://github.com/boostorg/interprocess/issues/49#issuecomment-369888615  

Thanks for the report.
