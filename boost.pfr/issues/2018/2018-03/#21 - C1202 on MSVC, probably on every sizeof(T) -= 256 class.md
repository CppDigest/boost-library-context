# #21 - C1202 on MSVC, probably on every sizeof(T) >= 256 class [Closed]

> Username: andj1210  
> Created at: 2018-03-25 17:52:50 UTC  
> Updated at: 2019-06-04 08:05:57 UTC  
> Closed at: 2019-06-04 08:05:02 UTC  
> Url: https://github.com/boostorg/pfr/issues/21  

your library is superb! Having that kind of "reflection light" is handy in so many places!  
  
I was going to make an experimental Config class, which can be reflected for Serialization to file or visualisation etc. At some point my MSVC compiler reports error C1202 when trying to call pfr::for_each_field on that class object. After some investigation it seems to appear when the reflected class reaches 256 Bytes. I.e. I can get this error by either adding more members or by increasing the size of each member.  
  
The attached example has 2 defines, if you set one of them, MSVC (VS2017 15.6.2) will emit C1202.  
I hope I didn´t make an error, since I´m not sure if my Cfg Templates do actually fit the requirements. I´m afraid its a compiler limitation w/o workarround...  
All I can tell is, if it compiles it works as expected. If you are interested to look into this and need more info please let me know. Thanks!  
  
[pfr_C1202.zip](https://github.com/apolukhin/magic_get/files/1845593/pfr_C1202.zip)

---

## Comment 1

> Username: apolukhin  
> Created at: 2018-12-03 06:08:27 UTC  
> Url: https://github.com/boostorg/pfr/issues/21#issuecomment-443599218  

I've added the test and it passes the checks after the PR #29.  
  
Could you please double check that the problem is gone?

---

## Comment 2

> Username: andj1210  
> Created at: 2018-12-12 20:14:38 UTC  
> Updated at: 2018-12-13 08:16:07 UTC  
> Url: https://github.com/boostorg/pfr/issues/21#issuecomment-446728226  

Sorry, the notification went into Spam.  
  
I downloaded the develop branch cf3094e, and repeated the test.  
It still fails in my Visual Studio 2017 15.8.8 (X86 Mode) with C1202 if the size gets 256.   
Do I need to do something else to get the patch?  
  
BTW: In error_pfr_c1202.cpp you need to uncomment line 28 to make the error appear.  
  
Update:  
I updated to Visual Studio 2017 15.9.4, now we have:  
"old" pfr with your updated error_pfr_c1202.cpp:  
siezof(T) < 256    OK  
siezof(T) >= 256 C1202  
  
cf3094e pfr:  
siezof(T) < 256    C1001  
siezof(T) >= 256 C1001  
  
-> the new version of MSVC crashes when compiling the new version of pfr (C1001 = internal compiler Error)  
"F:\TEMP\magic_get_fix\boost\pfr\detail\fields_count.hpp(92): fatal error C1001: Interner Compilerfehler.  
  (Compilerdatei "msc1.cpp", Zeile 1518)"  
  
Greetings,  
Andreas

---

## Comment 3

> Username: andj1210  
> Created at: 2019-03-17 11:34:31 UTC  
> Url: https://github.com/boostorg/pfr/issues/21#issuecomment-473656962  

I updated the testcode as follow:  
  
```  
#define BOOST_PFR_USE_CPP17 1  
#include <boost/pfr.hpp>  
#include <array>  
#include <stdint.h>  
  
struct BigStruct  
{  
   std::array<uint8_t, 255> arrayMember{}; // commit 6f35c87 -> OK  
   //std::array<uint8_t, 256> arrayMember{}; // commit 6f35c87 -> C1202  
};  
  
int main()   
{  
    BigStruct st;  
  
    boost::pfr::get<0>(st);  
}  
```  
  
Tests against (commit 6f35c87), the results:  
**Visual Studio 2017 15.9.9 -> /std:c++latest**  
std::array<uint8_t, 255> -> OK  
std::array<uint8_t, 256> -> C1202  
  
**Visual Studio 2017 15.9.9 -> /std:c++latest /permissive-**  
====================> Boost.PFR: Types with user specified constructors (non-aggregate initializable types) are not supported. boost\pfr\detail\fields_count.hpp	210  
  
**Visual Studio 2019 RC -> /std:c++latest** (with /permissive or /permissive- or without)  
====================> Boost.PFR: Types with user specified constructors (non-aggregate initializable types) are not supported. boost\pfr\detail\fields_count.hpp	210	  
  
<details>  
  <summary>Full Build Log VS2019 RC</summary>  
  >------ Alle neu erstellen gestartet: Projekt: magic_get_fix, Konfiguration: x64-Debug (Standard) ------  
  Cleaning... 2 files.  
  [1/2] C:\PROGRA~2\MIB055~1\2019\PROFES~1\VC\Tools\MSVC\1420~1.274\bin\HostX64\x64\cl.exe  /nologo /TP  -IF:\TEMP\magic_get_fix\. /std:c++latest /MDd /Zi /Ob0 /Od /RTC1 /showIncludes /FoCMakeFiles\error_pfr_c1202.dir\error_pfr_c1202.cpp.obj /FdCMakeFiles\error_pfr_c1202.dir\ /FS -c F:\TEMP\magic_get_fix\error_pfr_c1202.cpp  
  FAILED: CMakeFiles/error_pfr_c1202.dir/error_pfr_c1202.cpp.obj   
  C:\PROGRA~2\MIB055~1\2019\PROFES~1\VC\Tools\MSVC\1420~1.274\bin\HostX64\x64\cl.exe  /nologo /TP  -IF:\TEMP\magic_get_fix\. /std:c++latest /MDd /Zi /Ob0 /Od /RTC1 /showIncludes /FoCMakeFiles\error_pfr_c1202.dir\error_pfr_c1202.cpp.obj /FdCMakeFiles\error_pfr_c1202.dir\ /FS -c F:\TEMP\magic_get_fix\error_pfr_c1202.cpp  
F:\TEMP\magic_get_fix\boost\pfr\detail\fields_count.hpp(210): error C2338: ====================> Boost.PFR: Types with user specified constructors (non-aggregate initializable types) are not supported.  
  F:\TEMP\magic_get_fix\boost/pfr/detail/core17_generated.hpp(1036): note: Siehe Verweis auf die Instanziierung der gerade kompilierten Funktions-Vorlage "size_t boost::pfr::detail::fields_count<T>(void) noexcept".  
          with  
          [  
              T=BigStruct  
          ]  
  F:\TEMP\magic_get_fix\boost/pfr/precise/core.hpp(51): note: Siehe Verweis auf die Instanziierung der gerade kompilierten Funktions-Vorlage "auto boost::pfr::detail::tie_as_tuple<T>(T &) noexcept".  
          with  
          [  
              T=BigStruct  
          ]  
  F:\TEMP\magic_get_fix\error_pfr_c1202.cpp(26): note: Siehe Verweis auf die Instanziierung der gerade kompilierten Funktions-Vorlage "decltype(auto) boost::pfr::get<0,BigStruct>(T &) noexcept".  
          with  
          [  
              T=BigStruct  
          ]  
F:\TEMP\magic_get_fix\boost\pfr\detail\fields_count.hpp(215): error C2338: ====================> Boost.PFR: If there's no other failed static asserts then something went wrong. Please report this issue to the github along with the structure you're reflecting.  
F:\TEMP\magic_get_fix\boost\pfr\detail\sequence_tuple.hpp(115): error C2338: ====================> Boost.PFR: Tuple index out of bounds  
  F:\TEMP\magic_get_fix\boost/pfr/precise/core.hpp(51): note: Siehe Verweis auf die Instanziierung der gerade kompilierten Funktions-Vorlage "decltype(auto) boost::pfr::detail::sequence_tuple::get<0,>(boost::pfr::detail::sequence_tuple::tuple<> &&) noexcept".  
F:\TEMP\magic_get_fix\boost\pfr\detail\sequence_tuple.hpp(116): error C2672: "boost::pfr::detail::sequence_tuple::get_impl": keine übereinstimmende überladene Funktion gefunden  
F:\TEMP\magic_get_fix\boost\pfr\detail\sequence_tuple.hpp(114): error C2784: "T &boost::pfr::detail::sequence_tuple::get_impl(boost::pfr::detail::sequence_tuple::base_from_member<N,T> &) noexcept": Vorlage-Argument für "boost::pfr::detail::sequence_tuple::base_from_member<0,T> &" konnte nicht von "boost::pfr::detail::sequence_tuple::tuple<>" hergeleitet werden.  
  F:\TEMP\magic_get_fix\boost/pfr/detail/sequence_tuple.hpp(52): note: Siehe Deklaration von "boost::pfr::detail::sequence_tuple::get_impl"  
  ninja: build stopped: subcommand failed.  
  
Alle neu erstellen fehlgeschlagen.  
</details>

---

## Comment 4

> Username: andj1210  
> Created at: 2019-03-17 18:20:52 UTC  
> Updated at: 2019-03-17 18:21:53 UTC  
> Url: https://github.com/boostorg/pfr/issues/21#issuecomment-473691547  

We are not done yet...  
  
I figured in "/fpermissive-" the root cause of compilation failure is an issue determining the field count. With the following patches (workarrounds?) it works:  
  
```  
template <class T, std::size_t Begin, std::size_t Middle>  
constexpr std::size_t detect_fields_count(size_t_<Begin>, size_t_<Middle>, int) noexcept {      
	constexpr std::size_t next_v = (Begin + Middle) / 2;  
	using next_t = size_t_<next_v>;  
    return detail::detect_fields_count<T, Begin, next_v>(size_t_<Begin>{}, next_t{}, 1L);  
}  
```  
  
```  
template <class T, std::size_t N>  
constexpr std::size_t detect_fields_count_greedy(size_t_<N>, size_t_<N>) noexcept {  
    return detail::detect_fields_count_greedy_remember<T, N>(size_t_<N>{}, 1L);  
}  
  
```  
-> **Notice the explicit template argumrents for std::size_t**, otherwise it wouldnt select the   
" -> enable_if_constructible_helper_t<T, N>" versions. IMO it looks like a compiler bug and there is an issue extracting "N" from `size_t_<N>`.  
  
**Visual Studio 2017 15.9.9 + workarround -> /std:c++latest**  
std::array<uint8_t, 255> -> OK  
std::array<uint8_t, 256> -> C1202  
  
**Visual Studio 2017 15.9.9 + workarround  -> /std:c++latest /permissive-**  
std::array<uint8_t, 255> -> OK  
std::array<uint8_t, 512> -> OK  
std::array<uint8_t, 513> -> C1202  
  
**Visual Studio 2019 RC + workarround  -> /std:c++latest /permissive-**  
std::array<uint8_t, 255> -> OK  
std::array<uint8_t, 512> -> OK  
std::array<uint8_t, 513> -> C1202

---

## Comment 5

> Username: andj1210  
> Created at: 2019-03-19 18:12:49 UTC  
> Url: https://github.com/boostorg/pfr/issues/21#issuecomment-474506700  

As a last note, I experimenting a bit on a fork for now:  
https://github.com/andj1210/magic_get/tree/msvc_improvements  
  
C1202 was caused by make_index_sequence. Reducing the maximum assumed fields count made C1202 go away therefore. The sizeof(T) seems to be uncritical now.   
I´m not sure to miss something, since you already pointed to a commit which change the index / integer sequence.   
For unreasonable large field counts there are other problems (288 fields seems to be the next "magic" wall), but thats irrelevant IMO and needs further checking.

---

## Comment 6

> Username: apolukhin  
> Created at: 2019-05-04 09:39:06 UTC  
> Url: https://github.com/boostorg/pfr/issues/21#issuecomment-489311669  

I've added a CI run with `/permissive-` (it fails right now).  
  
I've tried to apply @andj1210 workarounds but avoided the `if constexpr` as it does not work in C++14. Unfortunately the CI build with `/permissive-` still fails.

---

## Comment 7

> Username: apolukhin  
> Created at: 2019-06-04 08:05:02 UTC  
> Url: https://github.com/boostorg/pfr/issues/21#issuecomment-498566858  

Fixed in https://github.com/apolukhin/magic_get/commit/82658caf7c283fdf843f815904e32f8dc93c9d89  
  
Now the library works on CI with `/permissive-`

---

## Comment 8

> Username: apolukhin  
> Created at: 2019-06-04 08:05:57 UTC  
> Url: https://github.com/boostorg/pfr/issues/21#issuecomment-498567127  

Thanks everyone for the investigations, tests and fixes!
