# #24 - Error with clang-cl.exe [Closed]

> Username: pdimov  
> Created at: 2018-11-13 19:42:29 UTC  
> Updated at: 2018-12-23 09:21:16 UTC  
> Closed at: 2018-12-23 09:21:16 UTC  
> Url: https://github.com/boostorg/type_index/issues/24  

While running the test suite of Function under the clang-win toolset (clang-cl.exe), I get  
  
```  
compile-c-c++ ..\..\bin.v2\libs\function\test\function_test_no_rtti.test\clang-w  
in-6.0.1\debug\rtti-off\threading-multi\function_test.obj  
In file included from test\function_test.cpp:10:  
In file included from ..\..\boost/test/minimal.hpp:53:  
In file included from ..\..\boost/test/impl/execution_monitor.ipp:28:  
In file included from ..\..\boost/test/execution_monitor.hpp:28:  
In file included from ..\..\boost/function/function0.hpp:11:  
In file included from ..\..\boost/function/detail/maybe_include.hpp:15:  
In file included from ..\..\boost/function/function_template.hpp:13:  
In file included from ..\..\boost/function/detail/prologue.hpp:17:  
In file included from ..\..\boost/function/function_base.hpp:21:  
In file included from ..\..\boost/type_index.hpp:41:  
In file included from ..\..\boost/type_index/ctti_type_index.hpp:22:  
..\..\boost/type_index/detail/compile_time_type_info.hpp(290,9):  error: no matc  
hing function for call to 'failed_to_get_function_name'  
        boost::typeindex::detail::failed_to_get_function_name();  
        ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
..\..\boost/type_index/ctti_type_index.hpp(118,44):  note: in instantiation of m  
ember function 'boost::detail::ctti<void>::n' requested here  
        : data_(boost::detail::ctti<void>::n())  
                                           ^  
..\..\boost/type_index/detail/compile_time_type_info.hpp(98,39):  note: candidat  
e template ignored: couldn't infer template argument 'T'  
    BOOST_CXX14_CONSTEXPR inline void failed_to_get_function_name() BOOST_NOEXCE  
PT {  
                                      ^  
```  
  
Is this a known issue?  
  
The test suite of `type_index` itself fails with similar errors. I also ran `msvc-14.1` as a sanity check, and it failed a few `constexpr` tests as well.

---

## Comment 1

> Username: apolukhin  
> Created at: 2018-11-14 06:05:12 UTC  
> Url: https://github.com/boostorg/type_index/issues/24#issuecomment-438548850  

There's definitely something wrong with clang-cl.exe. It does not find the macro for getting pretty function name https://github.com/boostorg/type_index/blob/c2caa340ab9d9d834c5ffafb3f267b3c46c8c821/include/boost/type_index/detail/compile_time_type_info.hpp#L276  
  
Could you please make sure that `__PRETTY_FUNCTION__` works on clang-cl?

---

## Comment 2

> Username: pdimov  
> Created at: 2018-11-14 07:53:55 UTC  
> Url: https://github.com/boostorg/type_index/issues/24#issuecomment-438570086  

```  
#include <boost/current_function.hpp>  
#include <iostream>  
  
int main()  
{  
	std::cout << BOOST_CURRENT_FUNCTION << std::endl;  
	std::cout << __PRETTY_FUNCTION__ << std::endl;  
	std::cout << __FUNCSIG__ << std::endl;  
}  
```  
Output:  
```  
main  
int main()  
int __cdecl main(void)  
```  
Sounds like `BOOST_CURRENT_FUNCTION` needs to be fixed there as well. :-)

---

## Comment 3

> Username: pdimov  
> Created at: 2018-11-14 07:59:29 UTC  
> Url: https://github.com/boostorg/type_index/issues/24#issuecomment-438571427  

msvc-14.1:  
```  
compile-c-c++ ..\..\bin.v2\libs\type_index\test\constexpr14_sort_check.test\msvc  
-14.1\debug\threading-multi\constexpr14_sort_check.obj  
constexpr14_sort_check.cpp  
C:\boost-git\develop\libs\type_index\examples\constexpr14_sort_check.cpp(60): er  
ror C2131: expression did not evaluate to a constant  
C:\boost-git\develop\boost/type_index/ctti_type_index.hpp(185): note: failure wa  
s caused by attempting to access a member on an object of dynamic type 'boost::t  
ypeindex::type_index_facade<boost::typeindex::ctti_type_index,boost::typeindex::  
detail::ctti_data>' in which the member is not defined  
C:\boost-git\develop\boost/type_index/ctti_type_index.hpp(185): note: see usage  
of 'boost::typeindex::ctti_type_index::data_'  
C:\boost-git\develop\libs\type_index\examples\constexpr14_sort_check.cpp(66): no  
te: see reference to function template instantiation 'void do_something<bool,dou  
ble,int>(const types<bool,double,int> &) noexcept' being compiled  
```

---

## Comment 4

> Username: apolukhin  
> Created at: 2018-11-15 18:48:26 UTC  
> Url: https://github.com/boostorg/type_index/issues/24#issuecomment-439148755  

MSVC constexpr failures are OK. I'll disable that test for MSVC as it does not have the required constexpr support.  
  
`BOOST_CURRENT_FUNCTION` bothers me much more. It is essential for some RTTI emulations in this library and in other libraries in Boost. Please fix it (i have no access to the clang-win toolset :( )  
  
BTW, what's the best way to detect clang-win toolset and version in source  codes?

---

## Comment 5

> Username: pdimov  
> Created at: 2018-11-15 19:07:02 UTC  
> Url: https://github.com/boostorg/type_index/issues/24#issuecomment-439154450  

I'm in the process of fixing it. Why no access? Don't you have Windows, or Appveyor? :-) (You just need to install LLVM.)  
  
If you want to detect the compiler itself, `__clang__` and `_MSC_VER`. If you want to detect the MS standard library, `_CPPLIB_VER`.

---

## Comment 6

> Username: pdimov  
> Created at: 2018-11-15 19:11:26 UTC  
> Url: https://github.com/boostorg/type_index/issues/24#issuecomment-439155810  

I still have failures with msvc-14.1, by the way:  
  
```  
C:\boost-git\develop\libs\type_index>b2 -j8 test | grep \.\.\.failed  
...failed compile-c-c++ ..\..\bin.v2\libs\type_index\test\constexpr14_sort_check  
.test\msvc-14.1\debug\threading-multi\constexpr14_sort_check.obj...  
...failed compile-c-c++ ..\..\bin.v2\libs\type_index\test\constexpr14_sort_check  
_no_rtti.test\msvc-14.1\debug\rtti-off\threading-multi\constexpr14_sort_check.ob  
j...  
...failed compile-c-c++ ..\..\bin.v2\libs\type_index\test\type_index_constexpr_t  
est.test\msvc-14.1\debug\threading-multi\type_index_constexpr_test.obj...  
...failed updating 3 targets...  
```  
  
This is the latest 2017, which is 19.16.

---

## Comment 7

> Username: apolukhin  
> Created at: 2018-11-15 23:00:15 UTC  
> Url: https://github.com/boostorg/type_index/issues/24#issuecomment-439221495  

> Don't you have Windows, or Appveyor?   
  
Yep, my CI scripts require updating. I'll do that someday after the 1.69 release

---

## Comment 8

> Username: pdimov  
> Created at: 2018-11-15 23:56:22 UTC  
> Url: https://github.com/boostorg/type_index/issues/24#issuecomment-439233486  

```  
compile-c-c++ ..\..\bin.v2\libs\type_index\test\type_index_constexpr_test.test\m  
svc-14.1\debug\threading-multi\type_index_constexpr_test.obj  
type_index_constexpr_test.cpp  
test\type_index_constexpr_test.cpp(97): error C2131: expression did not evaluate  
 to a constant  
C:\boost-git\develop\boost/type_index/ctti_type_index.hpp(185): note: failure wa  
s caused by attempting to access a member on an object of dynamic type 'boost::t  
ypeindex::type_index_facade<boost::typeindex::ctti_type_index,boost::typeindex::  
detail::ctti_data>' in which the member is not defined  
```

---

## Comment 9

> Username: apolukhin  
> Created at: 2018-12-23 09:21:16 UTC  
> Url: https://github.com/boostorg/type_index/issues/24#issuecomment-449624198  

Fixed in https://github.com/boostorg/type_index/commit/44ca756e3289cb11e90ec0fd0b30f3cbe56b40d4  
CI scripts were updated
