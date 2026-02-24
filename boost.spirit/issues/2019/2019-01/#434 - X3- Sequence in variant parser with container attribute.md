# #434 - X3: Sequence in variant parser with container attribute [Closed]

> Username: Xeverous  
> Created at: 2019-01-08 18:22:03 UTC  
> Updated at: 2020-06-04 14:17:10 UTC  
> Closed at: 2020-06-04 14:17:10 UTC  
> Url: https://github.com/boostorg/spirit/issues/434  

This problem originates from https://stackoverflow.com/questions/48390872 (not my question).  
  
SO attempt: http://coliru.stacked-crooked.com/a/56007d3f997b9748  
  
My attempt: http://coliru.stacked-crooked.com/a/3365b20994ce460e  
  
It seems that `\` is basically impossible to obtain, it's always skipped.  
  
Also, current X3 doc states that the attribute of `lit(c)` is unused and the attribute of `char_(c)` is the type of `c`. This does not seem to be the case as the first code sample has no difference between `lit(c)` and `char_(c)` versions.

---

## Comment 1

> Username: Kojoley  
> Created at: 2019-01-08 19:30:21 UTC  
> Updated at: 2019-01-08 23:32:49 UTC  
> Url: https://github.com/boostorg/spirit/issues/434#issuecomment-452422820  

MWE:  
```cpp  
#include <boost/spirit/home/x3.hpp>  
#include <iostream>  
#include <string>  
  
namespace x3 = boost::spirit::x3;  
  
int main()  
{  
    char const* s = "aaabcd", *const end = s + std::strlen(s);  
    std::string x;  
    if (!parse(s, end, *((x3::char_('a') >> x3::char_) | x3::char_), x))  
        std::cout << "failed\n";  
    else  
        std::cout << "result=" << x << '\n';  
}  
```  
Output:  
```  
result=abcd  
```  
https://wandbox.org/permlink/SSv343kksXESwQyU

---

## Comment 2

> Username: Kojoley  
> Created at: 2019-01-08 19:41:47 UTC  
> Url: https://github.com/boostorg/spirit/issues/434#issuecomment-452426408  

The actual problem should is sequence in variant parser with container attribute.  
The `((x3::char_ >> x3::char_) | x3::char_)` parser does not even compile with container attributes.  
With repeated parsers the sequence just returns the last value:  
```cpp  
#include <boost/spirit/home/x3.hpp>  
#include <iostream>  
#include <string>  
  
namespace x3 = boost::spirit::x3;  
  
int main()  
{  
    char const* s = "aaabcde", *const end = s + std::strlen(s);  
    std::string x;  
    if (!parse(s, end, *((x3::char_ >> x3::char_ >> x3::char_) | x3::char_), x))  
        std::cout << "failed\n";  
    else  
        std::cout << "result=" << x << '\n';  
}  
```  
Output:  
```  
result=ade  
```  
https://wandbox.org/permlink/4jdOsdfo5wcRhuXg

---

## Comment 3

> Username: Xeverous  
> Created at: 2019-01-08 23:24:19 UTC  
> Updated at: 2019-01-08 23:39:15 UTC  
> Url: https://github.com/boostorg/spirit/issues/434#issuecomment-452488552  

@Kojoley   
> @djowel what is the difference between &a and omit[a]? Ain't they the same thing?  
  
https://www.boost.org/doc/libs/1_69_0/libs/spirit/doc/x3/html/spirit_x3/quick_reference/directive.html  
https://www.boost.org/doc/libs/1_69_0/libs/spirit/doc/x3/html/spirit_x3/quick_reference/operator.html  
  
They are not.  
  
- `omit[a]` parses `a` and ignores it's attribute  
- `&a` parses `a`, ignores it's attribute but does not consume any input (this is look-ahead only)

---

## Comment 4

> Username: Kojoley  
> Created at: 2019-01-08 23:39:06 UTC  
> Updated at: 2019-01-08 23:39:18 UTC  
> Url: https://github.com/boostorg/spirit/issues/434#issuecomment-452491886  

Nevermind, I thought I wrote `&x3::char_('a') >> x3::char_ >> x3::char_) | x3::char_`.

---

## Comment 5

> Username: Kojoley  
> Created at: 2019-01-26 14:50:26 UTC  
> Url: https://github.com/boostorg/spirit/issues/434#issuecomment-457837050  

After boostorg/spirit#439 the examples above instead of compiling and silently doing something wrong will fire a static assert (the assert is for the user ill-formed code, but it founds a bug in Spirit so it is not that informative). They also perform backtracking and need explicit attribute recovery (see boostorg/spirit#835). For the both issues there is a workaround (and also a speedup hack) if the parser does not skip/dismiss an input - use `raw` directive.  
  
I also see that this and boostorg/spirit#394 are subsets of a general bug. I have some thoughts on it, but have some more urgent things to do.
