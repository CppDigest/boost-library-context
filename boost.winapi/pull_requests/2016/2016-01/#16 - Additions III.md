# #16 Additions III. [Closed]

> Username: klemens-morgenstern  
> Created at: 2016-01-27 19:23:34 UTC  
> Updated at: 2016-01-31 19:01:25 UTC  
> Closed at: 2016-01-31 17:16:19 UTC  
> Url: https://github.com/boostorg/winapi/pull/16  

Alright, third round. I hope I got all problems  
  
I also put the process_info stuff into the process.hpp  
  
Also, two questions concerning ShowWindow.hpp  
- Is the handling of HWND correct in ShowWindow?  
- The MSDN describes ShowWindow with int as parameter - is this alright?  
  
Concerning the travis.yml: I'll remove that as soon as you accept this or any subsequent PR; I'd just like to keep it in for the test.  
If you happen to choose to pull the travis-PR it doesn't matter, since the files are equal.  
  
Travis: [![res](https://travis-ci.org/klemens-morgenstern/winapi.svg)](https://travis-ci.org/klemens-morgenstern/winapi/builds)

---

## Comment 1

> Username: klemens-morgenstern  
> Created_at: 2016-01-28 22:09:52 UTC  
> Url: https://github.com/boostorg/winapi/pull/16#issuecomment-176443922  

@Lastique Would it fit into the style of the library, to make an alias for the STARTUPINFO_ type. I would make it the following way (though it's C++11):  
  
``` cpp  
template<typename CharType> struct startup_info_t;  
template<> struct basic_startup_info_t<CHAR_>    { typedef STARTUPINFOA_  type; };  
template<> struct basic_startup_info_t<WCHAR_> { typedef STARTUPINFOW_ type; };  
  
template<typename CharType>  
using basic_startup_info = typename basic_startup_info_t<CharType>::type;  
  
typedef basic_startup_info<CHAR_>    startup_info;  
typedef basic_startup_info<WCHAR_> wstartup_info;  
```  
  
That way it would look like the std-library, i.e. std::string and std::wstring.

---

## Comment 2

> Username: Lastique  
> Created_at: 2016-01-28 23:33:06 UTC  
> Url: https://github.com/boostorg/winapi/pull/16#issuecomment-176480223  

I don't think it would be a good idea. One of the goals of Boost.WinAPI is to be a lightweight and safe replacement for windows.h and not much more than that. Besides, I'm not sure if such type aliasing would be useful. Most Boost libraries will just use one structure (the wide character one is the most logical choice), I don't see a use case where one would want to select the type and related functions like that.

---

## Comment 3

> Username: klemens-morgenstern  
> Created_at: 2016-01-28 23:46:43 UTC  
> Url: https://github.com/boostorg/winapi/pull/16#issuecomment-176485336  

Ok, it might be needed in boost-process, though that's clear atm. I will then choose the class inside this module, not winapi.

---

## Comment 4

> Username: Lastique  
> Created_at: 2016-01-31 17:16:19 UTC  
> Url: https://github.com/boostorg/winapi/pull/16#issuecomment-177553427  

Committed a modified version to develop.

---

## Comment 5

> Username: klemens-morgenstern  
> Created_at: 2016-01-31 19:01:25 UTC  
> Url: https://github.com/boostorg/winapi/pull/16#issuecomment-177584470  

Thank you!

---
