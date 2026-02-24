# #520 - any_char parser accepts chars outside encoding [Closed]

> Username: sehe  
> Created at: 2019-07-07 14:47:27 UTC  
> Updated at: 2019-07-08 23:16:32 UTC  
> Closed at: 2019-07-08 23:16:32 UTC  
> Url: https://github.com/boostorg/spirit/issues/520  

The following assert fails:  
  
```c++  
#include <boost/spirit/home/x3.hpp>  
  
int main() {  
    namespace x3 = boost::spirit::x3;  
  
    char const* input = "\x80";  
    assert(!x3::parse(input, input+1, x3::ascii::char_));  
}  
```  
  
The assert should pass because `char_encoding::ascii::test` disallows non-7bit ASCII, so `"\x80"` is outside the encoding.  
  
The corresponding Qi test case DOES pass:  
  
```c++  
#include <boost/spirit/include/qi.hpp>  
  
int main() {  
    namespace qi = boost::spirit::qi;  
  
    char const* input = "\x80";  
    assert(!qi::parse(input, input+1, qi::ascii::char_));  
}  
```  
  
As noticed on stackoverflow https://stackoverflow.com/questions/56916592/spirit-x3-how-to-fail-parse-on-non-ascii-input

---

## Comment 1

> Username: sehe  
> Created at: 2019-07-07 15:20:13 UTC  
> Url: https://github.com/boostorg/spirit/issues/520#issuecomment-509007786  

Created a merge request the most logical fix, https://github.com/boostorg/spirit/pull/521  
  
Should not break any tests

---

## Comment 2

> Username: djowel  
> Created at: 2019-07-08 23:16:32 UTC  
> Url: https://github.com/boostorg/spirit/issues/520#issuecomment-509425983  

merged.
