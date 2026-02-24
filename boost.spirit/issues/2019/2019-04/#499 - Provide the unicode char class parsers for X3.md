# #499 - Provide the unicode char class parsers for X3. [Closed]

> Username: djowel  
> Created at: 2019-04-25 13:00:07 UTC  
> Updated at: 2019-08-26 02:34:22 UTC  
> Closed at: 2019-08-26 02:34:22 UTC  
> Url: https://github.com/boostorg/spirit/issues/499  

There are more hidden unicode facilities in spirit (e.g. see https://github.com/boostorg/spirit/blob/develop/include/boost/spirit/home/support/char_encoding/unicode.hpp). And there's a lot! These need to be aded to the char class parsers. Currently, only the basics are provided: https://github.com/boostorg/spirit/blob/develop/include/boost/spirit/home/x3/char/char_class.hpp  
  
There's no unicode for those yet.

---

## Comment 1

> Username: IggShaman  
> Created at: 2019-07-19 21:07:41 UTC  
> Updated at: 2019-07-19 21:11:33 UTC  
> Url: https://github.com/boostorg/spirit/issues/499#issuecomment-513378553  

Hi everybody, looks like I'm yet another soul looking for unicode support in x3. Is there any way I can help?  
  
Right now I am stuck trying to make x3 use e.g. unicode::isalnum(..) or standard_wide::isalnum(..), to no avail. For some reason it always resorts to standard::ischar(..):  
  
```  
#include <codecvt>  
#include <iomanip>  
#include <boost/config/warning_disable.hpp>  
#include <boost/spirit/home/x3.hpp>  
  
namespace x3 = boost::spirit::x3;  
  
namespace t {  
x3::rule<class a_string, std::u32string> const a_string = "a_string";  
auto const a_string_def = +x3::alnum;  
BOOST_SPIRIT_DEFINE(a_string);  
}  
  
std::string to_utf8(std::u32string input) {  
    std::wstring_convert<std::codecvt_utf8<char32_t>, char32_t> conv;  
    return conv.to_bytes(input);  
}  
  
int main() {  
    using iterator_type = std::u32string::const_iterator;  
      
    std::u32string result;  
    std::u32string input{U"hello привiт"};  
    iterator_type iter = input.begin();  
    iterator_type const end = input.end();  
    bool r = x3::parse(  
      iter,  
      end,  
      t::a_string,  
      result);  
      
    if (!r or iter != end) {  
        std::cerr << "Failed to parse\n";  
        return -1;  
    }  
      
    std::cout << "Parsing succeeded; result: [" << to_utf8(result) << "]\n";  
}  
```  
  
As a result, it fails when parsing non-ascii letters from my `input` `std::u32string`.

---

## Comment 2

> Username: Kojoley  
> Created at: 2019-07-20 10:42:27 UTC  
> Url: https://github.com/boostorg/spirit/issues/499#issuecomment-513456956  

@IggShaman you do not use unicode parsers in your example. `x3::alnum` is an alias of `x3::ascii::alnum`, you should use `x3::unicode::alnum` instead (to enable it you have to `#define BOOST_SPIRIT_X3_UNICODE`). Also, the input string contains a whitespace, and `alnum` does not allow them. After fixing those problems, the example passes https://wandbox.org/permlink/bd2IHAXCbjXQXKMq

---

## Comment 3

> Username: IggShaman  
> Created at: 2019-07-20 17:48:34 UTC  
> Updated at: 2019-07-20 17:49:10 UTC  
> Url: https://github.com/boostorg/spirit/issues/499#issuecomment-513486689  

Thanks @Kojoley, that helped. One small diff to `boost/spirit/home/x3/char/char.hpp` was needed to get things to compile with gcc 9.1:  
  
```  
--- char.hpp.pre        2019-07-20 10:47:31.889535552 -0700  
+++ char.hpp    2019-07-20 10:47:39.309560760 -0700  
@@ -56,7 +56,7 @@  
     namespace unicode  
     {  
         typedef any_char<char_encoding::unicode> char_type;  
-        auto const char_ = char_type{};  
+        //auto const char_ = char_type{};  
  
         inline literal_char<char_encoding::unicode, unused_type>  
         lit(char32_t ch)  
```

---

## Comment 4

> Username: djowel  
> Created at: 2019-07-21 00:05:19 UTC  
> Url: https://github.com/boostorg/spirit/issues/499#issuecomment-513507961  

> Thanks @Kojoley, that helped. One small diff to `boost/spirit/home/x3/char/char.hpp` was needed to get things to compile with gcc 9.1:  
  
What is the error? Why did you comment it out?

---

## Comment 5

> Username: IggShaman  
> Created at: 2019-07-23 05:28:50 UTC  
> Url: https://github.com/boostorg/spirit/issues/499#issuecomment-514059656  

Must've messed something up locally, it now compiles without commenting out anything
