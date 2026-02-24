# #709 - Fail to compile spirit/home/support/info.hpp with /std:c++latest on VS2022 [Closed]

> Username: yuxianch  
> Created at: 2022-01-05 07:41:11 UTC  
> Updated at: 2022-01-06 09:20:23 UTC  
> Closed at: 2022-01-06 09:20:23 UTC  
> Url: https://github.com/boostorg/spirit/issues/709  

## Summary  
Fail to compile spirit/home/support/info.hpp with /std:c++latest on VS2022 for error: field has incomplete type 'boost::spirit::info'.  
  
## Detail  
test.cpp:  
```  
#include <boost/spirit/home/support/info.hpp>  
```  
Use clang-cl to compile test.cpp and it is failed when compiling with /std:c++latest on VS2022:  
```  
# compile with /std:c++14 -> passed  
+ clang-cl -c test.cpp -TP /Z7 /Od /Ob0 /W3 /GR /MDd /EHs /std:c++14 -ID:/iusers/yuxianch/tc_test/boost_1_75_0/libs/convert/test/../include -ID:/iusers/yuxianch/tc_test/boost_1_75_0/libs/convert/test/../../..  
# compile with /std:c++latest -> failed  
+ clang-cl -c test.cpp -TP /Z7 /Od /Ob0 /W3 /GR /MDd /EHs /std:c++latest -ID:/iusers/yuxianch/tc_test/boost_1_75_0/libs/convert/test/../include -ID:/iusers/yuxianch/tc_test/boost_1_75_0/libs/convert/test/../../..  
In file included from test.cpp:1:  
In file included from D:/iusers/yuxianch/tc_test/boost_1_75_0/libs/convert/test/../../..\boost/spirit/home/support/info.hpp:14:  
In file included from D:/iusers/yuxianch/tc_test/boost_1_75_0/libs/convert/test/../../..\boost/variant/variant.hpp:21:  
In file included from D:/iusers/yuxianch/tc_test/boost_1_75_0/libs/convert/test/../../..\boost/type_index.hpp:29:  
In file included from D:/iusers/yuxianch/tc_test/boost_1_75_0/libs/convert/test/../../..\boost/type_index/stl_type_index.hpp:22:  
In file included from D:/iusers/yuxianch/tc_test/boost_1_75_0/libs/convert/test/../../..\boost/type_index/type_index_facade.hpp:14:  
In file included from c:/Program files/Microsoft Visual Studio/2022/Professional/VC/Tools/MSVC/14.30.30705/include\string:11:  
In file included from c:/Program files/Microsoft Visual Studio/2022/Professional/VC/Tools/MSVC/14.30.30705/include\xstring:14:  
In file included from c:/Program files/Microsoft Visual Studio/2022/Professional/VC/Tools/MSVC/14.30.30705/include\xmemory:16:  
In file included from c:/Program files/Microsoft Visual Studio/2022/Professional/VC/Tools/MSVC/14.30.30705/include\xutility:15:  
c:/Program files/Microsoft Visual Studio/2022/Professional/VC/Tools/MSVC/14.30.30705/include\utility(329,10): error:  
      field has incomplete type 'boost::spirit::info'  
    _Ty1 first; // the first stored value  
         ^  
c:/Program files/Microsoft Visual Studio/2022/Professional/VC/Tools/MSVC/14.30.30705/include\xutility(1999,32): note:  
      in instantiation of template class 'std::pair<boost::spirit::info, boost::spirit::info>' requested here  
            { _Fake_decay_copy(begin(__t)) } -> input_or_output_iterator;  
                               ^  
c:/Program files/Microsoft Visual Studio/2022/Professional/VC/Tools/MSVC/14.30.30705/include\xutility(1999,15): note:  
      in instantiation of requirement here  
            { _Fake_decay_copy(begin(__t)) } -> input_or_output_iterator;  
              ^~~~~~~~~~~~~~~~~~~~~~~~~~~~  
c:/Program files/Microsoft Visual Studio/2022/Professional/VC/Tools/MSVC/14.30.30705/include\xutility(1998,60): note:  
      while substituting template arguments into constraint expression here  
        concept _Has_ADL = _Has_class_or_enum_type<_Ty> && requires(_Ty __t) {  
                                                           ^~~~~~~~~~~~~~~~~~~  
c:/Program files/Microsoft Visual Studio/2022/Professional/VC/Tools/MSVC/14.30.30705/include\xutility(2018,38): note:  
      while checking the satisfaction of concept '_Has_ADL<const boost::recursive_wrapper<std::pair<boost::spirit::info,  
      boost::spirit::info>> &>' requested here  
                } else if constexpr (_Has_ADL<_Ty>) {  
                                     ^~~~~~~~~~~~~  
c:/Program files/Microsoft Visual Studio/2022/Professional/VC/Tools/MSVC/14.30.30705/include\xutility(2026,55): note:  
      in instantiation of function template specialization 'std::ranges::_Begin::_Cpo::_Choose<const  
      boost::recursive_wrapper<std::pair<boost::spirit::info, boost::spirit::info>> &>' requested here  
            static constexpr _Choice_t<_St> _Choice = _Choose<_Ty>();  
                                                      ^  
c:/Program files/Microsoft Visual Studio/2022/Professional/VC/Tools/MSVC/14.30.30705/include\xutility(2031,27): note:  
      (skipping 68 contexts in backtrace; use -ftemplate-backtrace-limit=0 to see all)  
                requires (_Choice<_Ty&>._Strategy != _St::_None)  
                          ^  
D:/iusers/yuxianch/tc_test/boost_1_75_0/libs/convert/test/../../..\boost/mpl/aux_/preprocessed/plain/and.hpp(50,7): note:  
      in instantiation of template class 'boost::mpl::aux::and_impl<true,  
      boost::mpl::not_<boost::is_const<boost::variant<boost::spirit::info::nil_, std::basic_string<char>,  
      boost::recursive_wrapper<boost::spirit::info>, boost::recursive_wrapper<std::pair<boost::spirit::info,  
      boost::spirit::info>>, boost::recursive_wrapper<std::list<boost::spirit::info>>>>>,  
      boost::detail::variant::is_variant_constructible_from<boost::variant<boost::spirit::info::nil_,  
      std::basic_string<char>, boost::recursive_wrapper<boost::spirit::info>,  
      boost::recursive_wrapper<std::pair<boost::spirit::info, boost::spirit::info>>,  
      boost::recursive_wrapper<std::list<boost::spirit::info>>> &&, boost::mpl::l_item<mpl_::long_<5>,  
      boost::spirit::info::nil_, boost::mpl::l_item<mpl_::long_<4>, std::basic_string<char>,  
      boost::mpl::l_item<mpl_::long_<3>, boost::recursive_wrapper<boost::spirit::info>,  
      boost::mpl::l_item<mpl_::long_<2>, boost::recursive_wrapper<std::pair<boost::spirit::info, boost::spirit::info>>,  
      boost::mpl::l_item<mpl_::long_<1>, boost::recursive_wrapper<std::list<boost::spirit::info>>,  
      boost::mpl::l_end>>>>>>, mpl_::bool_<true>, mpl_::bool_<true>>' requested here  
    : aux::and_impl<  
      ^  
D:/iusers/yuxianch/tc_test/boost_1_75_0/libs/convert/test/../../..\boost/core/enable_if.hpp(41,41): note: in  
      instantiation of template class  
      'boost::mpl::and_<boost::is_rvalue_reference<boost::variant<boost::spirit::info::nil_, std::basic_string<char>,  
      boost::recursive_wrapper<boost::spirit::info>, boost::recursive_wrapper<std::pair<boost::spirit::info,  
      boost::spirit::info>>, boost::recursive_wrapper<std::list<boost::spirit::info>>> &&>,  
      boost::mpl::not_<boost::is_const<boost::variant<boost::spirit::info::nil_, std::basic_string<char>,  
      boost::recursive_wrapper<boost::spirit::info>, boost::recursive_wrapper<std::pair<boost::spirit::info,  
      boost::spirit::info>>, boost::recursive_wrapper<std::list<boost::spirit::info>>>>>,  
      boost::detail::variant::is_variant_constructible_from<boost::variant<boost::spirit::info::nil_,  
      std::basic_string<char>, boost::recursive_wrapper<boost::spirit::info>,  
      boost::recursive_wrapper<std::pair<boost::spirit::info, boost::spirit::info>>,  
      boost::recursive_wrapper<std::list<boost::spirit::info>>> &&, boost::mpl::l_item<mpl_::long_<5>,  
      boost::spirit::info::nil_, boost::mpl::l_item<mpl_::long_<4>, std::basic_string<char>,  
      boost::mpl::l_item<mpl_::long_<3>, boost::recursive_wrapper<boost::spirit::info>,  
      boost::mpl::l_item<mpl_::long_<2>, boost::recursive_wrapper<std::pair<boost::spirit::info, boost::spirit::info>>,  
      boost::mpl::l_item<mpl_::long_<1>, boost::recursive_wrapper<std::list<boost::spirit::info>>,  
      boost::mpl::l_end>>>>>>>' requested here  
  struct enable_if : public enable_if_c<Cond::value, T> {};  
                                        ^  
D:/iusers/yuxianch/tc_test/boost_1_75_0/libs/convert/test/../../..\boost/variant/variant.hpp(2136,21): note: in  
      instantiation of template class  
      'boost::enable_if<boost::mpl::and_<boost::is_rvalue_reference<boost::variant<boost::spirit::info::nil_,  
      std::basic_string<char>, boost::recursive_wrapper<boost::spirit::info>,  
      boost::recursive_wrapper<std::pair<boost::spirit::info, boost::spirit::info>>,  
      boost::recursive_wrapper<std::list<boost::spirit::info>>> &&>,  
      boost::mpl::not_<boost::is_const<boost::variant<boost::spirit::info::nil_, std::basic_string<char>,  
      boost::recursive_wrapper<boost::spirit::info>, boost::recursive_wrapper<std::pair<boost::spirit::info,  
      boost::spirit::info>>, boost::recursive_wrapper<std::list<boost::spirit::info>>>>>,  
      boost::detail::variant::is_variant_constructible_from<boost::variant<boost::spirit::info::nil_,  
      std::basic_string<char>, boost::recursive_wrapper<boost::spirit::info>,  
      boost::recursive_wrapper<std::pair<boost::spirit::info, boost::spirit::info>>,  
      boost::recursive_wrapper<std::list<boost::spirit::info>>> &&, boost::mpl::l_item<mpl_::long_<5>,  
      boost::spirit::info::nil_, boost::mpl::l_item<mpl_::long_<4>, std::basic_string<char>,  
      boost::mpl::l_item<mpl_::long_<3>, boost::recursive_wrapper<boost::spirit::info>,  
      boost::mpl::l_item<mpl_::long_<2>, boost::recursive_wrapper<std::pair<boost::spirit::info, boost::spirit::info>>,  
      boost::mpl::l_item<mpl_::long_<1>, boost::recursive_wrapper<std::list<boost::spirit::info>>,  
      boost::mpl::l_end>>>>>>>, boost::variant<boost::spirit::info::nil_, std::basic_string<char>,  
      boost::recursive_wrapper<boost::spirit::info>, boost::recursive_wrapper<std::pair<boost::spirit::info,  
      boost::spirit::info>>, boost::recursive_wrapper<std::list<boost::spirit::info>>> &>' requested here  
    typename boost::enable_if<  
                    ^  
D:/iusers/yuxianch/tc_test/boost_1_75_0/libs/convert/test/../../..\boost/variant/variant.hpp(1105,7): note: while  
      substituting deduced template arguments into function template 'operator=' [with T =  
      boost::variant<boost::spirit::info::nil_, std::basic_string<char>, boost::recursive_wrapper<boost::spirit::info>,  
      boost::recursive_wrapper<std::pair<boost::spirit::info, boost::spirit::info>>,  
      boost::recursive_wrapper<std::list<boost::spirit::info>>>]  
class variant  
      ^  
D:/iusers/yuxianch/tc_test/boost_1_75_0/libs/convert/test/../../..\boost/spirit/home/support/info.hpp(29,12): note:  
      while declaring the implicit move assignment operator for 'info'  
    struct info  
           ^  
D:/iusers/yuxianch/tc_test/boost_1_75_0/libs/convert/test/../../..\boost/spirit/home/support/info.hpp(29,12): note:  
      definition of 'boost::spirit::info' is not complete until the closing '}'  
In file included from test.cpp:1:  
In file included from D:/iusers/yuxianch/tc_test/boost_1_75_0/libs/convert/test/../../..\boost/spirit/home/support/info.hpp:14:  
In file included from D:/iusers/yuxianch/tc_test/boost_1_75_0/libs/convert/test/../../..\boost/variant/variant.hpp:21:  
In file included from D:/iusers/yuxianch/tc_test/boost_1_75_0/libs/convert/test/../../..\boost/type_index.hpp:29:  
In file included from D:/iusers/yuxianch/tc_test/boost_1_75_0/libs/convert/test/../../..\boost/type_index/stl_type_index.hpp:22:  
In file included from D:/iusers/yuxianch/tc_test/boost_1_75_0/libs/convert/test/../../..\boost/type_index/type_index_facade.hpp:14:  
In file included from c:/Program files/Microsoft Visual Studio/2022/Professional/VC/Tools/MSVC/14.30.30705/include\string:11:  
In file included from c:/Program files/Microsoft Visual Studio/2022/Professional/VC/Tools/MSVC/14.30.30705/include\xstring:14:  
In file included from c:/Program files/Microsoft Visual Studio/2022/Professional/VC/Tools/MSVC/14.30.30705/include\xmemory:16:  
In file included from c:/Program files/Microsoft Visual Studio/2022/Professional/VC/Tools/MSVC/14.30.30705/include\xutility:15:  
c:/Program files/Microsoft Visual Studio/2022/Professional/VC/Tools/MSVC/14.30.30705/include\utility(330,10): error:  
      field has incomplete type 'boost::spirit::info'  
    _Ty2 second; // the second stored value  
         ^  
D:/iusers/yuxianch/tc_test/boost_1_75_0/libs/convert/test/../../..\boost/spirit/home/support/info.hpp(29,12): note:  
      definition of 'boost::spirit::info' is not complete until the closing '}'  
    struct info  
           ^  
2 errors generated.  
```  
Related code: https://github.com/boostorg/spirit/blob/166e71ae364915678bd9e1052f1347b8f5578283/include/boost/spirit/home/support/info.hpp#L22-L29

---

## Comment 1

> Username: Kojoley  
> Created at: 2022-01-05 15:02:40 UTC  
> Updated at: 2022-01-05 15:04:15 UTC  
> Url: https://github.com/boostorg/spirit/issues/709#issuecomment-1005762746  

I cannot reproduce:  
```  
G:\boost_1_78_0>type test.cpp  
#include <boost/spirit/home/support/info.hpp>  
  
G:\boost_1_78_0>clang-cl -c test.cpp -TP /Z7 /Od /Ob0 /W3 /GR /MDd /EHs /std:c++14 -I.  
  
G:\boost_1_78_0>clang-cl -c test.cpp -TP /Z7 /Od /Ob0 /W3 /GR /MDd /EHs /std:c++latest -I.  
  
G:\boost_1_78_0>clang-cl -c test.cpp -TP /Z7 /Od /Ob0 /W3 /GR /MDd /EHs /std:c++latest -I. -v  
clang version 13.0.0  
Target: x86_64-pc-windows-msvc  
Thread model: posix  
InstalledDir: C:\Program Files\LLVM\bin  
 (in-process)  
 "C:\\Program Files\\LLVM\\bin\\clang-cl.exe" -cc1 -triple x86_64-pc-windows-msvc19.30.30706 -emit-obj -mrelax-all -mincremental-linker-compatible --mrelax-relocations -disable-free -disable-llvm-verifier -discard-value-names -main-file-name test.cpp -mrelocation-model pic -pic-level 2 -mframe-pointer=none -relaxed-aliasing -fmath-errno -fno-rounding-math -mconstructor-aliases -munwind-tables -target-cpu x86-64 -mllvm -x86-asm-syntax=intel -tune-cpu generic -D_DEBUG -D_MT -D_DLL --dependent-lib=msvcrtd --dependent-lib=oldnames -stack-protector 2 -fcxx-exceptions -fexceptions -fms-volatile -fdiagnostics-format msvc -gno-column-info -gcodeview -debug-info-kind=constructor -v "-fcoverage-compilation-dir=G:\\boost-repo" -resource-dir "C:\\Program Files\\LLVM\\lib\\clang\\13.0.0" -I . -internal-isystem "C:\\Program Files\\LLVM\\lib\\clang\\13.0.0\\include" -internal-isystem "C:\\Program Files\\Microsoft Visual Studio\\2022\\Community\\VC\\Tools\\MSVC\\14.30.30705\\include" -internal-isystem "C:\\Program Files\\Microsoft Visual Studio\\2022\\Community\\VC\\Tools\\MSVC\\14.30.30705\\atlmfc\\include" -internal-isystem "C:\\Program Files (x86)\\Windows Kits\\10\\Include\\10.0.19041.0\\ucrt" -internal-isystem "C:\\Program Files (x86)\\Windows Kits\\10\\Include\\10.0.19041.0\\shared" -internal-isystem "C:\\Program Files (x86)\\Windows Kits\\10\\Include\\10.0.19041.0\\um" -internal-isystem "C:\\Program Files (x86)\\Windows Kits\\10\\Include\\10.0.19041.0\\winrt" -O0 -Wall -fdeprecated-macro "-fdebug-compilation-dir=G:\\boost-repo" -ferror-limit 19 -fmessage-length=120 -fno-use-cxa-atexit -fms-extensions -fms-compatibility -fms-compatibility-version=19.30.30706 -std=c++2b -fdelayed-template-parsing -fno-inline -fno-implicit-modules -fcolor-diagnostics -faddrsig -o test.obj -x c++ test.cpp  
clang -cc1 version 13.0.0 based upon LLVM 13.0.0 default target x86_64-pc-windows-msvc  
#include "..." search starts here:  
#include <...> search starts here:  
 .  
 C:\Program Files\LLVM\lib\clang\13.0.0\include  
 C:\Program Files\Microsoft Visual Studio\2022\Community\VC\Tools\MSVC\14.30.30705\include  
 C:\Program Files\Microsoft Visual Studio\2022\Community\VC\Tools\MSVC\14.30.30705\atlmfc\include  
 C:\Program Files (x86)\Windows Kits\10\Include\10.0.19041.0\ucrt  
 C:\Program Files (x86)\Windows Kits\10\Include\10.0.19041.0\shared  
 C:\Program Files (x86)\Windows Kits\10\Include\10.0.19041.0\um  
 C:\Program Files (x86)\Windows Kits\10\Include\10.0.19041.0\winrt  
End of search list.  
```

---

## Comment 2

> Username: yuxianch  
> Created at: 2022-01-06 09:20:23 UTC  
> Url: https://github.com/boostorg/spirit/issues/709#issuecomment-1006403545  

The issue can be only reproduced with developed version of clang-cl compiler.  
Close the issue as it could be compiler issue.
