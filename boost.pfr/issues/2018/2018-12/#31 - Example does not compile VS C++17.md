# #31 - Example does not compile VS C++17 [Closed]

> Username: simonrenger  
> Created at: 2018-12-29 17:49:49 UTC  
> Updated at: 2019-04-07 18:31:24 UTC  
> Closed at: 2019-04-07 14:46:09 UTC  
> Url: https://github.com/boostorg/pfr/issues/31  

The example:  
```cpp  
#include <iostream>  
#include <string>  
#include "boost/pfr/precise.hpp"  
  
struct some_person {  
    std::string name;  
    unsigned birth_year;  
};  
  
int main() {  
    some_person val{"Edgar Allan Poe", 1809};  
  
    std::cout << boost::pfr::get<0>(val)                // No macro!  
        << " was born in " << boost::pfr::get<1>(val);  // Works with any aggregate initializables!  
}  
```  
Does not compile:  
```text  
1>a:\cpp\reftest\reftest\ext\include\boost\pfr\detail\fields_count.hpp(209): error C2338: ====================> Boost.PFR: Types with user specified constructors (non-aggregate initializable types) are not supported.  
1>a:\cpp\reftest\reftest\ext\include\boost\pfr\detail\core17_generated.hpp(1036): note: see reference to function template instantiation 'size_t boost::pfr::detail::fields_count<T>(void) noexcept' being compiled  
1>        with  
1>        [  
1>            T=some_person  
1>        ]  
1>a:\cpp\reftest\reftest\ext\include\boost\pfr\precise\core.hpp(50): note: see reference to function template instantiation 'auto boost::pfr::detail::tie_as_tuple<T>(T &) noexcept' being compiled  
1>        with  
1>        [  
1>            T=some_person  
1>        ]  
1>a:\cpp\reftest\reftest\src\main.cpp(62): note: see reference to function template instantiation 'decltype(auto) boost::pfr::get<0,some_person>(T &) noexcept' being compiled  
1>        with  
1>        [  
1>            T=some_person  
1>        ]  
1>a:\cpp\reftest\reftest\ext\include\boost\pfr\detail\fields_count.hpp(214): error C2338: ====================> Boost.PFR: If there's no other failed static asserts then something went wrong. Please report this issue to the github along with the structure you're reflecting.  
1>a:\cpp\reftest\reftest\ext\include\boost\pfr\detail\sequence_tuple.hpp(114): error C2338: ====================> Boost.PFR: Tuple index out of bounds  
1>a:\cpp\reftest\reftest\ext\include\boost\pfr\precise\core.hpp(50): note: see reference to function template instantiation 'decltype(auto) boost::pfr::detail::sequence_tuple::get<0,>(boost::pfr::detail::sequence_tuple::tuple<> &&) noexcept' being compiled  
1>a:\cpp\reftest\reftest\ext\include\boost\pfr\detail\sequence_tuple.hpp(115): error C2672: 'get_impl': no matching overloaded function found  
1>a:\cpp\reftest\reftest\ext\include\boost\pfr\detail\sequence_tuple.hpp(113): error C2784: 'T &boost::pfr::detail::sequence_tuple::get_impl(boost::pfr::detail::sequence_tuple::base_from_member<N,T> &) noexcept': could not deduce template argument for 'boost::pfr::detail::sequence_tuple::base_from_member<0,T> &' from 'boost::pfr::detail::sequence_tuple::tuple<>'  
1>a:\cpp\reftest\reftest\ext\include\boost\pfr\detail\sequence_tuple.hpp(51): note: see declaration of 'boost::pfr::detail::sequence_tuple::get_impl'  
1>a:\cpp\reftest\reftest\src\main.cpp(62): error C2679: binary '<<': no operator found which takes a right-hand operand of type 'void' (or there is no acceptable conversion)  
```  
  
Compiler:  
```  
/JMC /permissive- /MP /GS /W3 /Zc:wchar_t /I"A:\cpp\reftest\reftest\ext\include\" /I"A:\cpp\reftest\\include\" /I"C:\Program Files (x86)\Visual Leak Detector\include" /ZI /Gm- /Od /sdl /Fd"x64\Debug\vc141.pdb" /Zc:inline /fp:precise /D "_MBCS" /errorReport:prompt /WX- /Zc:forScope /RTC1 /Gd /MDd /std:c++17 /FC /Fa"x64\Debug\" /EHsc /nologo /Fo"x64\Debug\" /Fp"x64\Debug\reftest.pch" /diagnostics:classic   
```

---

## Comment 1

> Username: apolukhin  
> Created at: 2019-01-08 10:30:45 UTC  
> Updated at: 2019-01-08 20:05:57 UTC  
> Url: https://github.com/boostorg/pfr/issues/31#issuecomment-452250395  

I've added more platforms and MSVC flags into the CI runs in commit https://github.com/apolukhin/magic_get/commit/e993693cb4fc604a4a3ec5b8f49e010105288099  
  
No issues were found. Probably your issue was fixed in commit https://github.com/apolukhin/magic_get/commit/9520aa703fd8d48ed56758878e40bdd80357173e .  
  
@simonrenger could you please double check that the issue still exist with the latest develop version of the library?

---

## Comment 2

> Username: simonrenger  
> Created at: 2019-01-08 22:17:44 UTC  
> Url: https://github.com/boostorg/pfr/issues/31#issuecomment-452472125  

I got latest of the develop branch... but I still get a compiler error  
  
Just to make sure I have created an empty C++ Solution with VS17  
  
The error remains:  
```  
1>------ Build started: Project: magic_getTest, Configuration: Debug x64 ------  
1>main.cpp  
1>a:\cpp\magic_get\include\boost\pfr\detail\fields_count.hpp(210): error C2338: ====================> Boost.PFR: Types with user specified constructors (non-aggregate initializable types) are not supported.  
1>a:\cpp\magic_get\include\boost\pfr\detail\core17_generated.hpp(1036): note: see reference to function template instantiation 'size_t boost::pfr::detail::fields_count<T>(void) noexcept' being compiled  
1>        with  
1>        [  
1>            T=some_person  
1>        ]  
1>a:\cpp\magic_get\include\boost\pfr\precise\core.hpp(51): note: see reference to function template instantiation 'auto boost::pfr::detail::tie_as_tuple<T>(T &) noexcept' being compiled  
1>        with  
1>        [  
1>            T=some_person  
1>        ]  
1>a:\cpp\magic_gettest\magic_gettest\main.cpp(13): note: see reference to function template instantiation 'decltype(auto) boost::pfr::get<0,some_person>(T &) noexcept' being compiled  
1>        with  
1>        [  
1>            T=some_person  
1>        ]  
1>a:\cpp\magic_get\include\boost\pfr\detail\fields_count.hpp(215): error C2338: ====================> Boost.PFR: If there's no other failed static asserts then something went wrong. Please report this issue to the github along with the structure you're reflecting.  
1>a:\cpp\magic_get\include\boost\pfr\detail\sequence_tuple.hpp(115): error C2338: ====================> Boost.PFR: Tuple index out of bounds  
1>a:\cpp\magic_get\include\boost\pfr\precise\core.hpp(51): note: see reference to function template instantiation 'decltype(auto) boost::pfr::detail::sequence_tuple::get<0,>(boost::pfr::detail::sequence_tuple::tuple<> &&) noexcept' being compiled  
1>a:\cpp\magic_get\include\boost\pfr\detail\sequence_tuple.hpp(116): error C2672: 'get_impl': no matching overloaded function found  
1>a:\cpp\magic_get\include\boost\pfr\detail\sequence_tuple.hpp(114): error C2784: 'T &boost::pfr::detail::sequence_tuple::get_impl(boost::pfr::detail::sequence_tuple::base_from_member<N,T> &) noexcept': could not deduce template argument for 'boost::pfr::detail::sequence_tuple::base_from_member<0,T> &' from 'boost::pfr::detail::sequence_tuple::tuple<>'  
1>a:\cpp\magic_get\include\boost\pfr\detail\sequence_tuple.hpp(52): note: see declaration of 'boost::pfr::detail::sequence_tuple::get_impl'  
1>a:\cpp\magic_gettest\magic_gettest\main.cpp(13): error C2679: binary '<<': no operator found which takes a right-hand operand of type 'void' (or there is no acceptable conversion)  
1>c:\program files (x86)\microsoft visual studio\2017\community\vc\tools\msvc\14.16.27023\include\ostream(508): note: could be 'std::basic_ostream<char,std::char_traits<char>> &std::basic_ostream<char,std::char_traits<char>>::operator <<(std::basic_streambuf<char,std::char_traits<char>> *)'  
1>c:\program files (x86)\microsoft visual studio\2017\community\vc\tools\msvc\14.16.27023\include\ostream(480): note: or       'std::basic_ostream<char,std::char_traits<char>> &std::basic_ostream<char,std::char_traits<char>>::operator <<(const void *)'  
1>c:\program files (x86)\microsoft visual studio\2017\community\vc\tools\msvc\14.16.27023\include\ostream(460): note: or       'std::basic_ostream<char,std::char_traits<char>> &std::basic_ostream<char,std::char_traits<char>>::operator <<(long double)'  
1>c:\program files (x86)\microsoft visual studio\2017\community\vc\tools\msvc\14.16.27023\include\ostream(440): note: or       'std::basic_ostream<char,std::char_traits<char>> &std::basic_ostream<char,std::char_traits<char>>::operator <<(double)'  
1>c:\program files (x86)\microsoft visual studio\2017\community\vc\tools\msvc\14.16.27023\include\ostream(420): note: or       'std::basic_ostream<char,std::char_traits<char>> &std::basic_ostream<char,std::char_traits<char>>::operator <<(float)'  
1>c:\program files (x86)\microsoft visual studio\2017\community\vc\tools\msvc\14.16.27023\include\ostream(400): note: or       'std::basic_ostream<char,std::char_traits<char>> &std::basic_ostream<char,std::char_traits<char>>::operator <<(unsigned __int64)'  
1>c:\program files (x86)\microsoft visual studio\2017\community\vc\tools\msvc\14.16.27023\include\ostream(380): note: or       'std::basic_ostream<char,std::char_traits<char>> &std::basic_ostream<char,std::char_traits<char>>::operator <<(__int64)'  
1>c:\program files (x86)\microsoft visual studio\2017\community\vc\tools\msvc\14.16.27023\include\ostream(360): note: or       'std::basic_ostream<char,std::char_traits<char>> &std::basic_ostream<char,std::char_traits<char>>::operator <<(unsigned long)'  
1>c:\program files (x86)\microsoft visual studio\2017\community\vc\tools\msvc\14.16.27023\include\ostream(340): note: or       'std::basic_ostream<char,std::char_traits<char>> &std::basic_ostream<char,std::char_traits<char>>::operator <<(long)'  
1>c:\program files (x86)\microsoft visual studio\2017\community\vc\tools\msvc\14.16.27023\include\ostream(320): note: or       'std::basic_ostream<char,std::char_traits<char>> &std::basic_ostream<char,std::char_traits<char>>::operator <<(unsigned int)'  
1>c:\program files (x86)\microsoft visual studio\2017\community\vc\tools\msvc\14.16.27023\include\ostream(295): note: or       'std::basic_ostream<char,std::char_traits<char>> &std::basic_ostream<char,std::char_traits<char>>::operator <<(int)'  
1>c:\program files (x86)\microsoft visual studio\2017\community\vc\tools\msvc\14.16.27023\include\ostream(275): note: or       'std::basic_ostream<char,std::char_traits<char>> &std::basic_ostream<char,std::char_traits<char>>::operator <<(unsigned short)'  
1>c:\program files (x86)\microsoft visual studio\2017\community\vc\tools\msvc\14.16.27023\include\ostream(241): note: or       'std::basic_ostream<char,std::char_traits<char>> &std::basic_ostream<char,std::char_traits<char>>::operator <<(short)'  
1>c:\program files (x86)\microsoft visual studio\2017\community\vc\tools\msvc\14.16.27023\include\ostream(221): note: or       'std::basic_ostream<char,std::char_traits<char>> &std::basic_ostream<char,std::char_traits<char>>::operator <<(bool)'  
1>c:\program files (x86)\microsoft visual studio\2017\community\vc\tools\msvc\14.16.27023\include\ostream(215): note: or       'std::basic_ostream<char,std::char_traits<char>> &std::basic_ostream<char,std::char_traits<char>>::operator <<(std::ios_base &(__cdecl *)(std::ios_base &))'  
1>c:\program files (x86)\microsoft visual studio\2017\community\vc\tools\msvc\14.16.27023\include\ostream(209): note: or       'std::basic_ostream<char,std::char_traits<char>> &std::basic_ostream<char,std::char_traits<char>>::operator <<(std::basic_ios<char,std::char_traits<char>> &(__cdecl *)(std::basic_ios<char,std::char_traits<char>> &))'  
1>c:\program files (x86)\microsoft visual studio\2017\community\vc\tools\msvc\14.16.27023\include\ostream(204): note: or       'std::basic_ostream<char,std::char_traits<char>> &std::basic_ostream<char,std::char_traits<char>>::operator <<(std::basic_ostream<char,std::char_traits<char>> &(__cdecl *)(std::basic_ostream<char,std::char_traits<char>> &))'  
1>c:\program files (x86)\microsoft visual studio\2017\community\vc\tools\msvc\14.16.27023\include\ostream(502): note: or       'std::basic_ostream<char,std::char_traits<char>> &std::basic_ostream<char,std::char_traits<char>>::operator <<<void>(std::nullptr_t)'  
1>c:\program files (x86)\microsoft visual studio\2017\community\vc\tools\msvc\14.16.27023\include\ostream(702): note: or       'std::basic_ostream<char,std::char_traits<char>> &std::operator <<<char,std::char_traits<char>>(std::basic_ostream<char,std::char_traits<char>> &,const char *)'  
1>c:\program files (x86)\microsoft visual studio\2017\community\vc\tools\msvc\14.16.27023\include\ostream(749): note: or       'std::basic_ostream<char,std::char_traits<char>> &std::operator <<<char,std::char_traits<char>>(std::basic_ostream<char,std::char_traits<char>> &,char)'  
1>c:\program files (x86)\microsoft visual studio\2017\community\vc\tools\msvc\14.16.27023\include\ostream(787): note: or       'std::basic_ostream<char,std::char_traits<char>> &std::operator <<<std::char_traits<char>>(std::basic_ostream<char,std::char_traits<char>> &,const char *)'  
1>c:\program files (x86)\microsoft visual studio\2017\community\vc\tools\msvc\14.16.27023\include\ostream(834): note: or       'std::basic_ostream<char,std::char_traits<char>> &std::operator <<<std::char_traits<char>>(std::basic_ostream<char,std::char_traits<char>> &,char)'  
1>c:\program files (x86)\microsoft visual studio\2017\community\vc\tools\msvc\14.16.27023\include\ostream(960): note: or       'std::basic_ostream<char,std::char_traits<char>> &std::operator <<<std::char_traits<char>>(std::basic_ostream<char,std::char_traits<char>> &,const signed char *)'  
1>c:\program files (x86)\microsoft visual studio\2017\community\vc\tools\msvc\14.16.27023\include\ostream(967): note: or       'std::basic_ostream<char,std::char_traits<char>> &std::operator <<<std::char_traits<char>>(std::basic_ostream<char,std::char_traits<char>> &,signed char)'  
1>c:\program files (x86)\microsoft visual studio\2017\community\vc\tools\msvc\14.16.27023\include\ostream(974): note: or       'std::basic_ostream<char,std::char_traits<char>> &std::operator <<<std::char_traits<char>>(std::basic_ostream<char,std::char_traits<char>> &,const unsigned char *)'  
1>c:\program files (x86)\microsoft visual studio\2017\community\vc\tools\msvc\14.16.27023\include\ostream(981): note: or       'std::basic_ostream<char,std::char_traits<char>> &std::operator <<<std::char_traits<char>>(std::basic_ostream<char,std::char_traits<char>> &,unsigned char)'  
1>c:\program files (x86)\microsoft visual studio\2017\community\vc\tools\msvc\14.16.27023\include\ostream(1047): note: or       'std::basic_ostream<char,std::char_traits<char>> &std::operator <<<char,std::char_traits<char>>(std::basic_ostream<char,std::char_traits<char>> &,const std::error_code &)'  
1>a:\cpp\magic_gettest\magic_gettest\main.cpp(14): note: while trying to match the argument list '(std::ostream, void)'  
1>a:\cpp\magic_get\include\boost\pfr\detail\sequence_tuple.hpp(114): error C2784: 'T &boost::pfr::detail::sequence_tuple::get_impl(boost::pfr::detail::sequence_tuple::base_from_member<N,T> &) noexcept': could not deduce template argument for 'boost::pfr::detail::sequence_tuple::base_from_member<1,T> &' from 'boost::pfr::detail::sequence_tuple::tuple<>'  
1>a:\cpp\magic_get\include\boost\pfr\detail\sequence_tuple.hpp(52): note: see declaration of 'boost::pfr::detail::sequence_tuple::get_impl'  
1>Done building project "magic_getTest.vcxproj" -- FAILED.  
========== Build: 0 succeeded, 1 failed, 0 up-to-date, 0 skipped ==========  
```

---

## Comment 3

> Username: apolukhin  
> Created at: 2019-01-09 13:28:37 UTC  
> Url: https://github.com/boostorg/pfr/issues/31#issuecomment-452696347  

@simonrenger could you please run the tests using b2.exe from Boost? I'd like to make sure, that the failure is caused by a change in MSVC internals, rather than by some flag or environmental change.  
  
Do the other examples compile?

---

## Comment 4

> Username: dimarusyy  
> Created at: 2019-01-09 20:24:27 UTC  
> Url: https://github.com/boostorg/pfr/issues/31#issuecomment-452843731  

I'm getting the same error. See the log in attached file after feeding tests to b2.  
[log.zip](https://github.com/apolukhin/magic_get/files/2742489/log.zip)

---

## Comment 5

> Username: andj1210  
> Created at: 2019-03-17 11:43:07 UTC  
> Url: https://github.com/boostorg/pfr/issues/31#issuecomment-473657526  

The error appears by /permissive- in VS2017, in VS2019 its always persent!  
See: https://github.com/apolukhin/magic_get/issues/21#issuecomment-473656962

---

## Comment 6

> Username: janwilmans  
> Created at: 2019-03-25 14:19:00 UTC  
> Updated at: 2019-03-25 14:29:32 UTC  
> Url: https://github.com/boostorg/pfr/issues/31#issuecomment-476217209  

This specific example does work, when you make sure /std:c++17 **_is_** specified and /permissive- is **_not_** specified.  
  
However, the tests from test/flat/flat_for_each_field.cpp do not compile on vs2017 nor vs2019 even with /std:latest. Looking at the logs these **tests are also not running in CI,** I see destructuring_tie.cpp passing, but none of the other tests below:  
  
```  
core.cpp  
destructuring_tie.cpp  // this is the only test that I see running in the log file on appveyor   
flat_for_each_field.cpp  
flat_motivating_example.cpp  
flat_motivating_example2.cpp  
flat_tuple_size.cpp  
flat_tuple_size_on_bitfields.cpp  
flat_tuple_size_on_non_aggregate.cpp  
  
```  
We should close this issue, as the example here _is_ actually working, I've opened a new issue #37 for the CI problem.

---

## Comment 7

> Username: apolukhin  
> Created at: 2019-04-07 14:46:09 UTC  
> Url: https://github.com/boostorg/pfr/issues/31#issuecomment-480596639  

Closing in favor of #37

---

## Comment 8

> Username: apolukhin  
> Created at: 2019-04-07 14:48:51 UTC  
> Url: https://github.com/boostorg/pfr/issues/31#issuecomment-480596855  

I'd appreciate reporting the `/std:c++17 + /permissive-` issue to the MSVC development team if anyone is interested in that combination of flags.

---

## Comment 9

> Username: janwilmans  
> Created at: 2019-04-07 18:31:24 UTC  
> Url: https://github.com/boostorg/pfr/issues/31#issuecomment-480617281  

I'd be happy to report anything, however, /permissive- means to the compiler adheres strictly to the standard's rules and disables microsoft specific extensions. That said, _a lot_ has changed during the last 2 years in the msvc compiler. They finally support two-phase-lookup and expression SFINAE and according to their website, they were fully c++17 compliant last year.
