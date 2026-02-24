# #391 - Leftover workaround configuration option breaking std::codecvt<char16_t> and std::codecvt<char32_t> on Windows [Closed]

> Username: Xerus2  
> Created at: 2021-08-04 20:06:44 UTC  
> Updated at: 2021-08-19 16:15:02 UTC  
> Closed at: 2021-08-19 15:50:48 UTC  
> Url: https://github.com/boostorg/config/issues/391  

It looks like this code was added because MSVC did not support the full std::codecvt specializations at the time:  
https://github.com/boostorg/config/blob/c3de80c2efa69c10dcad7c7ffa37047c3d4cf49e/include/boost/config/stdlib/dinkumware.hpp#L232  
  
The MS links in that comment are dead, but there's reports about the MSVC issue here: https://social.msdn.microsoft.com/Forums/en-US/8f40dcd8-c67f-4eba-9134-a19b9178e481/vs-2015-rc-linker-stdcodecvt-error and https://social.msdn.microsoft.com/Forums/windowsdesktop/ru-RU/999a5b68-a1d3-4a76-8f3b-65655257c301/vs2017-stdcodecvt-linker-error?forum=vcgeneral  
  
Setting this macro then leads into https://github.com/boostorg/log/blob/0d4ffdd1adf24abb62ee8ac434b47497c58b424f/include/boost/log/detail/config.hpp#L101  
Which is commented as:  
"The compiler does not support std::codecvt<char16_t> and std::codecvt<char32_t> specializations. The BOOST_NO_CXX11_HDR_CODECVT means there's no usable codecvt header, which is slightly different from this macro. But in order for <codecvt> to be implemented the std::codecvt specializations have to be implemented as well."  
  
This config flag is still active as of Boost 1.76, since it appears that _CPPLIB_VER is still equal to 650 at this time and the config code in dinkumware.hpp hasn't been touched since then. I have verified that _MSC_VER >= 1922 links successfully, so I recommend changing this check to look for _MSC_VER < 1922.

---

## Comment 1

> Username: jzmaddock  
> Created at: 2021-08-15 17:51:34 UTC  
> Url: https://github.com/boostorg/config/issues/391#issuecomment-899088098  

Apologies for the delay, unfortunately changing this leads to:  
  
error C4996: 'std::codecvt_utf8': warning STL4017: std::wbuffer_convert, std::wstring_convert, and the <codecvt> header (containing std::codecvt_mode, std::codecvt_utf8, std::codecvt_utf16, and std::codecvt_utf8_utf16) are deprecated in C++17. (The std::codecvt class template is NOT deprecated.) The C++ Standard doesn't provide equivalent non-deprecated functionality; consider using MultiByteToWideChar() and WideCharToMultiByte() from <Windows.h> instead. You can define _SILENCE_CXX17_CODECVT_HEADER_DEPRECATION_WARNING or _SILENCE_ALL_CXX17_DEPRECATION_WARNINGS to acknowledge that you have received this warning.  
  
In C++17 and later.  
  
So.... I need to fix this for C++14 builds, but for later std versions, the macro _should_ be defined (albeit with updated docs), since <codecvt> is not supported post C++17.

---

## Comment 2

> Username: Xerus2  
> Created at: 2021-08-15 18:49:49 UTC  
> Url: https://github.com/boostorg/config/issues/391#issuecomment-899094660  

It's a bit of a quandary, unfortunately. The <codecvt> header was indeed deprecated in C++17, as you're seeing with that warning. However, the header and the related functions are still present and available for use in C++17 (and MSVC can link against them since the bug was fixed), and the Standard has not yet provided a replacement. So it's not quite a "header doesn't exist" situation, it's "header exists but is deprecated." Downstream Boost modules (Boost.Log in my case) are still making use of this header and its functions, so the current situation means that Boost.Log is able to do stuff like this: https://github.com/boostorg/log/blob/8a880720be5a990fdd0f864244bfff1a39a69d83/include/boost/log/sources/record_ostream.hpp#L236 on Linux and Mac, but not on Windows, so I have to provide my own overloaded << operator for char16_t and char32_t on that platform only. So I would either just acknowledge the warning and disable the macro on MSVC 1922 or higher, or define that macro for C++17 on all platforms, not just Windows. If you do that, though, it will probably break Boost.Log (and maybe other Boost libraries), as I understand it.

---

## Comment 3

> Username: jzmaddock  
> Created at: 2021-08-17 16:45:59 UTC  
> Url: https://github.com/boostorg/config/issues/391#issuecomment-900458989  

I don't think we have any choice but to define the macro: new msvc projects enable SDL checks by default which renders this a hard error.  This is also the route we have taken with BOOST_NO_AUTO_PTR, BOOST_NO_CXX11_ATOMIC_SMART_PTR and a few others.  
  
gcc libstdc++ and clang libc++ are taking a slightly different route, but note that libc++ is removing features at the same time as the std does, which I assume will be C++2b.  The referenced PR handles this, Boost.Log I suspect should be using a BOOST_WORKAROUND here for what is a slightly different use case.

---

## Comment 4

> Username: Xerus2  
> Created at: 2021-08-17 17:07:12 UTC  
> Url: https://github.com/boostorg/config/issues/391#issuecomment-900478057  

Should I open a ticket to have Boost.Log investigate this and implement the BOOST_WORKAROUND, then?

---

## Comment 5

> Username: jzmaddock  
> Created at: 2021-08-19 15:50:47 UTC  
> Url: https://github.com/boostorg/config/issues/391#issuecomment-902027400  

PR for Boost.Log here: https://github.com/boostorg/log/pull/159.

---

## Comment 6

> Username: Xerus2  
> Created at: 2021-08-19 16:07:31 UTC  
> Updated at: 2021-08-19 16:15:02 UTC  
> Url: https://github.com/boostorg/config/issues/391#issuecomment-902039977  

Thanks, I opened https://github.com/boostorg/log/issues/160 before I saw your PR for Boost.Log. Log doesn't appear to be using any of the deprecated codecvt features, so setting BOOST_LOG_NO_CXX11_CODECVT_FACETS simply based on BOOST_NO_CXX11_HDR_CODECVT doesn't seem correct. BOOST_LOG_NO_CXX11_CODECVT_FACETS should only be set if we're on a pre-C++11 compiler or a broken MSVC version, which I believe is >= 1900 && < 1922.
