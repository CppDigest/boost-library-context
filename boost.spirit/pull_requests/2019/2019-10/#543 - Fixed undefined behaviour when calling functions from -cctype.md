# #543 Fixed undefined behaviour when calling functions from <cctype> [Closed]

> Username: wmamrak  
> Created at: 2019-10-09 22:00:52 UTC  
> Updated at: 2019-10-12 11:41:03 UTC  
> Closed at: 2019-10-12 03:26:31 UTC  
> Url: https://github.com/boostorg/spirit/pull/543  

It is required to cast ch from signed char to unsigned char, see:  
https://en.cppreference.com/w/cpp/string/byte/islower#Notes

---

## Comment 1

> Username: wmamrak  
> Created_at: 2019-10-09 22:01:08 UTC  
> Url: https://github.com/boostorg/spirit/pull/543#issuecomment-540221197  

Fixes assertion failure of ch being < 0 on Visual Studio 2019 for the following test case:  
  
```  
#include <boost/spirit/home/x3.hpp>  
using namespace boost::spirit::x3;  
  
int main() {  
    std::string txt = "ą";  
  
    auto b = txt.begin();  
    auto e = txt.end();  
  
    parse(b, e, no_case['a']);  
  
    return 0;  
}  
```  
  
Important note: the file must be saved with UTF-8 encoding without BOM.

---

## Comment 2

> Username: djowel  
> Created_at: 2019-10-10 02:21:45 UTC  
> Url: https://github.com/boostorg/spirit/pull/543#issuecomment-540300674  

I don't think that is the issue, or the solution here (including the link you gave). The caller already receives an int and passes the int. It's probably somewhere outside the char_encoding traits. I'll have a look.  
  
Side note: std::isxxx(ch) ? true : false; is odd, style-wise. I don't know when that started. It should just be std::isxxx(ch), without the conditional.

---

## Comment 3

> Username: wmamrak  
> Created_at: 2019-10-10 09:29:30 UTC  
> Updated_at: 2019-10-10 09:31:04 UTC  
> Url: https://github.com/boostorg/spirit/pull/543#issuecomment-540483962  

I am not claiming it is the perfect solution, most notably I don't like the types of char_encoding::standard are ints. But even if suboptimal, I would still call it a solution, since it solves given issue :-). To my knowledge, it is also least intrusive.  
  
In my example, the code point "ą" consists in UTF-8 encoding of 2 code units: 0xc4 and 0x85.  
The no_case_directive::parse calls char_parser::parse, which in turn dereferences string iterator, which returns a char = -60. This char is then passed to literal_char::test and then to no_case_compare::operator(). The latter calls functions of char_encoding::standard, promoting our char to int (still = -60). char_encoding::standard makes several calls to functions from cctype. Then the internal assertion fails, because it expects the given int to be in the range [0, 255].  
Casting int to unsigned char (in char_encoding::standard) before promoting it to int (in std::isxxx(ch)) changes int's value back to the correct 0xc4. The more I think about the proposed solution, the less suboptimal it looks to me :-).  
  
Regarding the side note: I noticed that odd code as well, but I didn't want to mix a bugfix with a minor, clean up fix. Also, correct version would be std::isxxx(ch) != 0, as per https://docs.microsoft.com/en-us/cpp/error-messages/compiler-warnings/compiler-warning-level-3-c4800?f1url=https%3A%2F%2Fmsdn.microsoft.com%2Fquery%2Fdev16.query%3FappId%3DDev16IDEF1%26l%3DEN-US%26k%3Dk(C4800)%26rd%3Dtrue&view=vs-2019

---

## Comment 4

> Username: djowel  
> Created_at: 2019-10-10 13:02:30 UTC  
> Url: https://github.com/boostorg/spirit/pull/543#issuecomment-540566801  

> Also, correct version would be std::isxxx(ch) != 0  
  
Perhaps that's the reason for the std::isxxx(ch) ? true : false;  
  
... I'll look into this as soon as I can.

---

## Comment 5

> Username: djowel  
> Created_at: 2019-10-10 13:11:43 UTC  
> Url: https://github.com/boostorg/spirit/pull/543#issuecomment-540571931  

> In my example, the code point "ą" consists in UTF-8 encoding of 2 code units: 0xc4 and 0x85.  
> The no_case_directive::parse calls char_parser::parse  
  
Aside: Oh and BTW, you shouldn't pass utf8 as-is. Instead use a unicode iterator (e.g. the ones in boost regex) and the unicode encoding. no_case does not make sense for utf8 encoded string, unlike the unicode encoding.

---

## Comment 6

> Username: wmamrak  
> Created_at: 2019-10-10 14:43:26 UTC  
> Url: https://github.com/boostorg/spirit/pull/543#issuecomment-540620986  

I assumed X3's behaviour did not change since version 2. In version 2, if I am not mistaken, literal_char parser transformed the argument of no_case ("a" in my example) to its lower and upper versions, and then made the comparison to characters in the input string, whatever that input string encoding was. It passed all of the exhaustive tests, so I assumed there are no reasons to add overhead of using utf8-aware iterators.

---

## Comment 7

> Username: wmamrak  
> Created_at: 2019-10-10 21:07:24 UTC  
> Url: https://github.com/boostorg/spirit/pull/543#issuecomment-540796764  

To sum the above up, as long as none, or only one character from input string and no_case's argument is utf8-encoded, matching should work.

---

## Comment 8

> Username: djowel  
> Created_at: 2019-10-11 03:09:38 UTC  
> Url: https://github.com/boostorg/spirit/pull/543#issuecomment-540881611  

> I assumed X3's behaviour did not change since version 2. In version 2, if I am not mistaken, literal_char parser transformed the argument of no_case ("a" in my example) to its lower and upper versions, and then made the comparison to characters in the input string, whatever that input string encoding was. It passed all of the exhaustive tests, so I assumed there are no reasons to add overhead of using utf8-aware iterators.  
  
Yes, that is correct. However, Qi nor X3 does not expand utf8 (which can comprise multiple bytes for a single code point) automatically. What you want for utf8 is the unicode encoding, but you have to do the utf8 to ucs4 decoding before hand using an iterator adaptor such as the ones in boost.regex.  
  
(Note: no_case in that case makes use of http://bit.ly/33m8lD8)  
  
See https://github.com/cierelabs/json_spirit for an example on how to correctly parse utf8.

---

## Comment 9

> Username: djowel  
> Created_at: 2019-10-11 03:12:52 UTC  
> Url: https://github.com/boostorg/spirit/pull/543#issuecomment-540882233  

To sum the above, if you need to parse utf8 correctly, you need to use the unicode encoding and the utf8-aware iterators. Having said that, this issue 543 is valid and should be fixed.

---

## Comment 10

> Username: djowel  
> Created_at: 2019-10-11 03:35:34 UTC  
> Updated_at: 2019-10-11 03:36:15 UTC  
> Url: https://github.com/boostorg/spirit/pull/543#issuecomment-540886651  

> To sum the above, if you need to parse utf8 correctly, you need to use the unicode encoding and the utf8-aware iterators. Having said that, this issue 543 is valid and should be fixed.  
  
OK, let me relax that a little bit. After syncing my thoughts, I realised that on utf8 inputs, you only need the utf8-aware iterators IF you are using the character classification parsers, including the no_case directive. Why? Because otherwise, using only the standard encoding, you cannot convert unicode code-points such as this example: 'Ž' -> 'ž'  
  
In the json parser I gave a link to above, the unicode iterators are actually just needed for converting json escaped strings, but all else simply use the ascii encoding.

---

## Comment 11

> Username: wmamrak  
> Created_at: 2019-10-11 08:14:52 UTC  
> Url: https://github.com/boostorg/spirit/pull/543#issuecomment-540964025  

> on utf8 inputs, you only need the utf8-aware iterators IF you are using the character classification parsers, including the no_case directive  
  
Yes, but for the no_case directive this is even less strict, i.e. utf8-aware iterators are needed only IF input string and no_case's argument BOTH contain utf8 characters.

---

## Comment 12

> Username: wmamrak  
> Created_at: 2019-10-11 11:59:40 UTC  
> Updated_at: 2019-10-11 12:00:16 UTC  
> Url: https://github.com/boostorg/spirit/pull/543#issuecomment-541034694  

Upon further investigation I found a related issue. Assume I want to parse a utf8 input with an ascii::space skipper:  
```  
#include <boost/spirit/home/x3.hpp>  
using namespace boost::spirit::x3;  
  
int main() {  
    std::string txt = "ą";  
  
    auto b = txt.begin();  
    auto e = txt.end();  
  
    phrase_parse(b, e, no_case['a'], ascii::space);  
  
    return 0;  
}  
```  
Since it is a classifier, as you said, parsing will fail with an assertion in  
boost/spirit/home/support/char_encoding/ascii.hpp  
in the function ascii::isspace (line 264 atm).  
Yet again it is a utf8 code unit > 127, stored as a char in a std::string (hence < 0), then casted to int.  
One can solve this in a few ways:  
1. write a custom parser which does not call boost::spirit::char_encoding::ascii::isspace or any function from cctype  
2. use literal_char<char_encoding::ascii, unused_type> skipper = ' '; which is awful  
3. introduce a cast in boost/spirit/home/support/char_encoding/ascii.hpp similarly to what I proposed in this PR.  
4. do something more intrusive that 3  
5. use unicode::space . This will bloat your executable significantly and introduce additional complexity thus slowing it down, for no reason really.

---

## Comment 13

> Username: wmamrak  
> Created_at: 2019-10-11 13:56:59 UTC  
> Url: https://github.com/boostorg/spirit/pull/543#issuecomment-541075407  

Related: 22040cb3b22c589be66bf610b258a8da44d4613d

---

## Comment 14

> Username: djowel  
> Created_at: 2019-10-12 03:26:31 UTC  
> Url: https://github.com/boostorg/spirit/pull/543#issuecomment-541279370  

Fixed in https://github.com/boostorg/spirit/commit/f10cf8a80f31926e73d180b81bcb721db4b7cfd8  
(with safety support in https://github.com/boostorg/spirit/commit/5a217a5e033d34b016fef41ee95e311bc1a24140)

---

## Comment 15

> Username: wmamrak  
> Created_at: 2019-10-12 09:11:35 UTC  
> Url: https://github.com/boostorg/spirit/pull/543#issuecomment-541304914  

The core of the issue remained, though:  
```  
#include <boost/spirit/home/x3.hpp>  
using namespace boost::spirit::x3;  
  
int main() {  
    std::string txt = "ą";  
  
    assert(!std::isdigit((unsigned char)txt[0]));  
    assert(!std::isdigit((unsigned char)txt[1]));  
  
    auto b = txt.begin();  
    auto e = txt.end();  
  
    assert(!parse(b, e, digit));  
  
    return 0;  
}  
```

---

## Comment 16

> Username: djowel  
> Created_at: 2019-10-12 11:41:03 UTC  
> Url: https://github.com/boostorg/spirit/pull/543#issuecomment-541316877  

I'll look into it later. I suggest you post a complete test case that I can add to the test suite. Please make another issue for it.

---
