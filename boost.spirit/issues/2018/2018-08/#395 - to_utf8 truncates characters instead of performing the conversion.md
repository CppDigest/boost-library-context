# #395 - to_utf8 truncates characters instead of performing the conversion [Closed]

> Username: isnullxbh  
> Created at: 2018-08-28 08:28:28 UTC  
> Updated at: 2018-10-30 11:48:54 UTC  
> Closed at: 2018-10-30 11:48:54 UTC  
> Url: https://github.com/boostorg/spirit/issues/395  

https://github.com/boostorg/spirit/blob/925f40a2d0af5beed5c9a6d5882271d5d326e949/include/boost/spirit/home/support/utf8.hpp#L66  
  
Is it permissible - convert wchar_t to char (in the case of type of the input parameter is std::wstring)?

---

## Comment 1

> Username: Kojoley  
> Created at: 2018-08-28 12:13:28 UTC  
> Updated at: 2018-10-28 10:39:12 UTC  
> Url: https://github.com/boostorg/spirit/issues/395#issuecomment-416561361  

Ummm... yes, it is permissible, but it truncates the chars.  
However, this 10+ years old code written by @hkaiser is used only inside `Parser::what` functions (parser debug function which returns the parser name and values it was parametrized with).

---

## Comment 2

> Username: Kojoley  
> Created at: 2018-10-25 19:34:36 UTC  
> Url: https://github.com/boostorg/spirit/issues/395#issuecomment-433177879  

I have looked more into `to_utf8` and cannot find any problem there. It casts `wchar_t` to `unsigned short` and then assigns the value to an iterator. Can you provide an example?  
  
This one works as expected:  
```cpp  
#include <boost/spirit/home/support/utf8.hpp>  
#include <string>  
  
int main()  
{  
    std::wstring s = L"привет";  
    return "\xd0\xbf\xd1\x80\xd0\xb8\xd0\xb2\xd0\xb5\xd1\x82" != boost::spirit::to_utf8(s);  
}  
```

---

## Comment 3

> Username: isnullxbh  
> Created at: 2018-10-26 07:34:37 UTC  
> Updated at: 2018-10-26 08:15:35 UTC  
> Url: https://github.com/boostorg/spirit/issues/395#issuecomment-433315710  

Can you try to execute next code on Windows machine?  
  
```  
#include <boost/spirit/home/support/utf8.hpp>  
#include <string>  
  
int main()  
{  
    std::wstring s = L"𠼭";  
    return "\xf0\xa0\xbc\xad" ==  boost::spirit::to_utf8(s);  
}  
```

---

## Comment 4

> Username: Kojoley  
> Created at: 2018-10-26 11:21:34 UTC  
> Url: https://github.com/boostorg/spirit/issues/395#issuecomment-433375717  

I think the problem is in you string literal. On Windows `wchar_t` is 16-bit while the char in your literal `u20F2D` does not fit into 16-bit (134957 > 65535).  
  
Try to run:  
  
```cpp  
#include <boost/spirit/home/support/utf8.hpp>  
#include <string>  
#include <iostream>  
  
int main()  
{  
    std::wstring s = L"𠼭";  
    for (auto c : s) std::wcout << +c << '\n';  
    std::wcout << L"'" << s << L"'\n";  
    return "\xf0\xa0\xbc\xad" == boost::spirit::to_utf8(s);  
}  
```  
  
https://wandbox.org/permlink/AGJqDmp1kngplN0X

---

## Comment 5

> Username: Kojoley  
> Created at: 2018-10-28 14:50:43 UTC  
> Url: https://github.com/boostorg/spirit/issues/395#issuecomment-433712373  

I made a research and there is a problem in `to_utf8`.  
  
### The problem  
  
From the [\[lex.ccon\]/6](http://eel.is/c++draft/lex.ccon#6):  
  
> A character literal that begins with the letter `L`, such as `L'z'`, is a *wide-character literal*. A wide-character literal has type `wchar_­t`.<sup>[18](http://eel.is/c++draft/lex.ccon#footnote-18)</sup> The value of a wide-character literal containing a single *c-char* has value equal to the numerical value of the encoding of the *c-char* in the execution wide-character set, unless the *c-char* has no representation in the execution wide-character set, in which case the value is implementation-defined. [ *Note:* The type `wchar_­t` is able to represent all members of the execution wide-character set (see [\[basic.fundamental\]](http://eel.is/c++draft/basic.fundamental)). — *end note* ] The value of a wide-character literal containing multiple *c-chars* is implementation-defined.  
  
  
  
```cpp  
#include <iostream>  
#include <string>  
  
int main()  
{  
    using namespace std::literals;  
    std::cout << "sizeof(wchar_t): " << sizeof(wchar_t) << '\n';  
    std::cout << "string literal size: " << L"𠼭"s.size() << '\n';  
}  
```  
  
Linux (GCC 8.2/Clang 7)  
```  
sizeof(wchar_t): 4  
string literal size: 1  
```  
  
Windows (MSVC 14.1/GCC 8.2/Clang 7):  
```  
sizeof(wchar_t): 2  
string literal size: 2  
```  
  
On Windows the content of wchar_t string literal seems to be UTF-16, and this is where the problem come. Spirit does not do charset conversions, it simply feeds the data to `utf8_output_iterator` which expects UCS-4. This is definitely a bug, but I cannot say how it will be addressed, because in my opinion dealing with `wchar_t` is not worth the efforts. Ignoring the problem is a way to go, because this function is cosmetic one and is used only in parser debugging. Full character conversion support is too much as for me, it will bring more dependencies with little benefits. A compromise solution would be to assume UCS-4 for Unix and UTF-16 for Windows and this one I would rather go.  
  
### What you can do  
  
If you want a UTF-8 encoded string from the string literal, you would better to use the C++11 `u8` prefix <sup>[\[lex.ccon\]/3](http://eel.is/c++draft/lex.ccon#3)</sup>, it will give you a compile time UTF-8 string while your code will have the actual character literals. If you need a unicode string literal, use the C++11 `U` (uppercase!) prefix <sup>[\[lex.ccon\]/4](http://eel.is/c++draft/lex.ccon#4)</sup>. It will give you a UCS-4 string and Spirit works flawlessly with it on all platforms.  
  
```cpp  
#include <boost/core/lightweight_test.hpp>  
#include <boost/spirit/home/support/utf8.hpp>  
#include <iostream>  
  
int main()  
{  
    auto s = U"𠼭";  
    BOOST_TEST_EQ("\xf0\xa0\xbc\xad", boost::spirit::to_utf8(s));  
    BOOST_TEST_CSTR_EQ("\xf0\xa0\xbc\xad", u8"𠼭");  
    return boost::report_errors();  
}  
```

---

## Comment 6

> Username: Kojoley  
> Created at: 2018-10-28 15:03:28 UTC  
> Url: https://github.com/boostorg/spirit/issues/395#issuecomment-433713481  

@isnullxbh Can you please check if #413 solves the problem for you?

---

## Comment 7

> Username: isnullxbh  
> Created at: 2018-10-30 07:08:59 UTC  
> Url: https://github.com/boostorg/spirit/issues/395#issuecomment-434194023  

Hi, @Kojoley! Thanks a lot for publishing your research results! I can't check it on Windows machine at the moment, but I saw your merge and I'm trusting to tests you have written.
