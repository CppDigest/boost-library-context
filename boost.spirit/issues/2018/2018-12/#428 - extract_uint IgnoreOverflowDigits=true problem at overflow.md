# #428 - extract_uint IgnoreOverflowDigits=true problem at overflow [Closed]

> Username: Kojoley  
> Created at: 2018-12-19 18:47:59 UTC  
> Updated at: 2019-02-22 12:13:03 UTC  
> Closed at: 2019-02-22 12:13:03 UTC  
> Url: https://github.com/boostorg/spirit/issues/428  

There is a bug either in `extract_uint` or real parser.  
  
From my point of view the bug is in `extract_uint` because:  
  1. `IgnoreOverflowDigits` was added in the same commit as it usage in real parser 6832dc229872d7f9573a506cfe1ed640dc79871e  
  2. The `4294967296` either should result in `429496729` or `4294967290` but the iterator must be incremented.  
  
```cpp  
#include <boost/spirit/include/qi.hpp>  
  
namespace qi = boost::spirit::qi;  
  
int main()  
{  
    {  
        char const* s = "0.4294967296", *const e = s + std::strlen(s);  
        std::cout << "=== float_ ===\n";  
        std::cout << "input:    " << s << '\n';  
        float f;  
        if (qi::parse(s, e, qi::float_, f))  
            std::cout << "result:   " << f << '\n'  
                      << "expected: " << 0.4294967296f << '\n';  
        else  
            std::cout << "failed to parse\n";  
    }  
    {  
        char const* s = "4294967296", *const e = s + std::strlen(s);  
        std::cout << "=== extract_uint ===\n";  
        std::cout << "input:    " << s << '\n';  
        boost::uint32_t ul;  
        boost::spirit::qi::extract_uint<boost::uint32_t, 10, 1, -1, false, true>::call(s, e, ul);  
        std::cout << "result:   " << ul << '\n';  
        std::cout << "at end:   " << std::boolalpha << (s == e) << '\n';  
        std::cout << "chars left: " << (e - s) << '\n';  
    }  
}  
```  
Output:  
```  
=== float_ ===  
input:    0.4294967296  
result:   4.29497  
expected: 0.429497  
=== extract_uint ===  
input:    4294967296  
result:   4294967290  
at end:   false  
chars left: 1  
```  
https://wandbox.org/permlink/chIdPnbDOtS5FUat

---

## Comment 1

> Username: djowel  
> Created at: 2018-12-19 23:43:21 UTC  
> Url: https://github.com/boostorg/spirit/issues/428#issuecomment-448792779  

That's a very bad bug! How did the tests not catch that :-( ? Do you have a fix?

---

## Comment 2

> Username: djowel  
> Created at: 2018-12-19 23:58:58 UTC  
> Url: https://github.com/boostorg/spirit/issues/428#issuecomment-448796333  

Ah because IgnoreOverflowDigits is normally false (default) which the int parsers use.

---

## Comment 3

> Username: Kojoley  
> Created at: 2018-12-20 00:02:06 UTC  
> Updated at: 2018-12-20 00:37:58 UTC  
> Url: https://github.com/boostorg/spirit/issues/428#issuecomment-448797371  

Yes I have a fix https://github.com/Kojoley/spirit/commit/3dc361a88b6b26d4be2cff7d29464580529074d1 but I want to benchmark it before firing a PR.

---

## Comment 4

> Username: djowel  
> Created at: 2018-12-20 00:33:08 UTC  
> Url: https://github.com/boostorg/spirit/issues/428#issuecomment-448807867  

Wonderful! Please add the test case.
