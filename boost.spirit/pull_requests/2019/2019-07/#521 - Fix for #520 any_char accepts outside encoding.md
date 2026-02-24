# #521 Fix for #520 any_char accepts outside encoding [Merged]

> Username: sehe  
> Created at: 2019-07-07 15:18:55 UTC  
> Updated at: 2019-07-10 23:22:08 UTC  
> Merged at: 2019-07-08 23:15:30 UTC  
> Closed at: 2019-07-08 23:15:31 UTC  
> Url: https://github.com/boostorg/spirit/pull/521  

The following assert fails:  
  
    int main() {  
        namespace x3 = boost::spirit::x3;  
  
        char const* input = "\x80";  
        assert(!x3::parse(input, input+1, x3::ascii::char_));  
    }  
  
The assert should pass because char_encoding::ascii::test disallows  
non-7bit ASCII, so "\x80" is outside the encoding.  
  
The corresponding Qi test case DOES pass:  
  
    int main() {  
        namespace qi = boost::spirit::qi;  
  
        char const* input = "\x80";  
        assert(!qi::parse(input, input+1, qi::ascii::char_));  
    }  
  
This simple fix requires the encoding's `ischar` to return true always,  
making the behavior as specified and consistent with Qi and docs.

---

## Comment 1

> Username: djowel  
> Created_at: 2019-07-07 22:21:39 UTC  
> Url: https://github.com/boostorg/spirit/pull/521#issuecomment-509035264  

Environment: APPVEYOR_BUILD_WORKER_IMAGE=Visual Studio 2015, ADDRMDL=64, TOOLSET=msvc-14.0 seems to be failing. Hrmmm...

---

## Comment 2

> Username: sehe  
> Created_at: 2019-07-08 17:03:16 UTC  
> Url: https://github.com/boostorg/spirit/pull/521#issuecomment-509309488  

@djowel Looks unrelated. Only on that version of the compiler, is a compilation error anyways.  
  
The same error occurs on other recent builds like https://ci.appveyor.com/project/Kojoley/spirit-jod4k/builds/25691801 (PR 511)

---

## Comment 3

> Username: djowel  
> Created_at: 2019-07-08 23:15:18 UTC  
> Url: https://github.com/boostorg/spirit/pull/521#issuecomment-509425675  

Yeah, looks unrelated. I'll go merge this one.

---

## Comment 4

> Username: Kojoley  
> Created_at: 2019-07-10 18:11:45 UTC  
> Url: https://github.com/boostorg/spirit/pull/521#issuecomment-510170230  

Did you intentionally make `ascii::char_` always failing for iterators with bigger types than `char`, for example with UTF-32:  
  
```cpp  
#include <boost/spirit/home/x3.hpp>  
#include <string>  
#include <iostream>  
  
int main()  
{  
    std::u32string s = U"ascii";  
    bool r = parse(s.begin(), s.end(), +boost::spirit::x3::ascii::char_);  
    std::cout << std::boolalpha << r << '\n';  
}  
```  
  
It is a legitimate usage, is not it?

---

## Comment 5

> Username: djowel  
> Created_at: 2019-07-10 23:22:08 UTC  
> Url: https://github.com/boostorg/spirit/pull/521#issuecomment-510266589  

> Did you intentionally make `ascii::char_` always failing for iterators with bigger types than `char`, for example with UTF-32:  
  
Not sure. But, when in doubt, check what Qi code does.

---
