# #83 Fix narrowing. [Closed]

> Username: DanielaE  
> Created at: 2017-12-24 11:45:05 UTC  
> Updated at: 2018-05-17 20:24:26 UTC  
> Closed at: 2018-05-17 20:24:26 UTC  
> Url: https://github.com/boostorg/serialization/pull/83  

Signed-off-by: Daniela Engert <dani@ngrt.de>

---

## Comment 1

> Username: robertramey  
> Created_at: 2018-05-01 17:54:45 UTC  
> Url: https://github.com/boostorg/serialization/pull/83#issuecomment-385739872  

The file unescape.hpp contains the text:  
  
// class used by text archives to translate char strings to wchar_t  
// strings of the currently selected locale  
  
which suggests to me that the value type should wide type in any case.  Also look through the code I can't find where the Derived type wouldn't be a wide type.  Could you tell which tests emits this error, the compiler and switches used to reproduce it?

---

## Comment 2

> Username: DanielaE  
> Created_at: 2018-05-02 05:38:59 UTC  
> Url: https://github.com/boostorg/serialization/pull/83#issuecomment-385869961  

`test_iterators` exhibits this narrowing issue with every version of MSVC as the Boost test matrix clearly shows. The other compilers don't, but just by pure luck because they happen to compile without narrowing warnings enabled.

---

## Comment 3

> Username: robertramey  
> Created_at: 2018-05-02 16:06:53 UTC  
> Url: https://github.com/boostorg/serialization/pull/83#issuecomment-386031466  

OK - found it at xml_unescape line # 45.  This was part of a workaround for msvc 7.1.  Since msvc 7.1 is no longer supported (since about 10 years ago!).  I just eliminated that work around.  This should address the issue.

---

## Comment 4

> Username: robertramey  
> Created_at: 2018-05-05 18:17:25 UTC  
> Url: https://github.com/boostorg/serialization/pull/83#issuecomment-386824994  

Damn - turns out that the ICE crops up for all microsoft compilers of versions < 14

---
