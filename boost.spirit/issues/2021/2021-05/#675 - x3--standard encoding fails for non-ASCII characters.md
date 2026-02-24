# #675 - x3::standard encoding fails for non-ASCII characters [Closed]

> Username: 3dyd  
> Created at: 2021-05-06 08:14:30 UTC  
> Updated at: 2021-05-24 16:06:07 UTC  
> Closed at: 2021-05-24 16:06:07 UTC  
> Url: https://github.com/boostorg/spirit/issues/675  

I am failing to parse `char*` streams, which may contain UTF-8 sequences, using `standard` character encoding. Everything works fine until rules contain any of the character classification parsers.  
  
Minimal example:  
```  
#include <boost/spirit/home/x3.hpp>  
  
int main(int argc, char** argv)  
{  
    char ch = '\xE3';  
    parse(&ch, &ch + 1, boost::spirit::x3::digit);  
}  
```  
Example fails here (`ch == -29`):  
https://github.com/boostorg/spirit/blob/e30073e5bebb8102c73d1ad3588bf3f68cec2b64/include/boost/spirit/home/support/char_encoding/standard.hpp#L74  
  
Some digging leads to these lines:  
https://github.com/boostorg/spirit/blob/e30073e5bebb8102c73d1ad3588bf3f68cec2b64/include/boost/spirit/home/x3/char/char_class.hpp#L23-L33  
  
Shouldn't `Encoding::classify_type` be used in `cast_char`?  
  
This is only relevant for `standard` character encoding. For others these characters are either filtered out by `ischar` precondition (ascii) or `classify_type` is the same as `char_type` (others).

---

## Comment 1

> Username: Kojoley  
> Created at: 2021-05-21 16:23:07 UTC  
> Url: https://github.com/boostorg/spirit/issues/675#issuecomment-846080123  

> I am failing to parse `char*` streams, which may contain UTF-8 sequences, using `standard` character encoding.  
  
I do not think it is supported. How `std::isdigit` can work with multibyte encodings? You also did not set the locale to UTF-8.  
  
> Shouldn't `Encoding::classify_type` be used in `cast_char`?  
  
That might be so. I need to check how it would affect other encodings.

---

## Comment 2

> Username: 3dyd  
> Created at: 2021-05-23 11:20:35 UTC  
> Url: https://github.com/boostorg/spirit/issues/675#issuecomment-846546516  

> I do not think it is supported. How std::isdigit can work with multibyte encodings? You also did not set the locale to UTF-8.  
  
All bytes in multi-byte UTF-8 characters are always in [128..255] range, and single-byte UTF-8 characters are identical to ASCII, so for default C locale the character classification parsers will match ASCII characters and never match any bytes from multi-byte UTF-8 characters. This is exactly what I need.  
  
My use case is very similar to the one you've referenced. There is ASCII-based markup and UTF-8 literals in places defined by this markup. I.e. parser does not make any decisions basing on non-ASCII characters (they are put into AST field as is, or just the range is extracted (`x3::raw`), or they are simply ignored depending on the surrounding markup). So, while `boost::u8_to_u32_iterator` works fine, it introduces runtime overhead (and makes the code slightly more verbose) which is completely unreasonable in this case.  
  
> That might be so. I need to check how it would affect other encodings.  
  
FYI, I thought Qi and X3 share the same implementation, but apparently not in this case, and Qi has already been fixed: 6821c820.

---

## Comment 3

> Username: 3dyd  
> Created at: 2021-05-23 11:39:22 UTC  
> Url: https://github.com/boostorg/spirit/issues/675#issuecomment-846548862  

Here is workaround for existing code base (to be placed after X3 includes), in case anyone steps into the same issue and has to stick with specific Boost version:  
  
```  
#ifdef X3_CLASSIFY_WORKAROUND  
namespace boost { namespace spirit { namespace x3 {  
  
struct classify_workaround: char_encoding::standard  
{  
	typedef unsigned char char_type;  
};  
  
template <>  
struct char_class_base<char_encoding::standard>: char_class_base<classify_workaround>  
{};  
  
}}}  
#endif // X3_CLASSIFY_WORKAROUND  
```

---

## Comment 4

> Username: Kojoley  
> Created at: 2021-05-23 21:39:48 UTC  
> Url: https://github.com/boostorg/spirit/issues/675#issuecomment-846628246  

> single-byte UTF-8 characters are identical to ASCII  
  
It is definitely wrong. Most likely you meant the reverse thing, that ASCII is UTF-8 subset.  
  
> so for default C locale the character classification parsers will match ASCII characters and never match any bytes from multi-byte UTF-8 characters. This is exactly what I need.  
  
Is not it exactly opposite? Your parser will fail on the first non-ASCII codepoint.  
  
> FYI, I thought Qi and X3 share the same implementation  
  
Qi and X3 implementations are mostly independent with a small shared part.  
  
> Qi has already been fixed: 6821c8208b0d763aabb17f79a7be9b3714f6c80c  
  
I remember that PR. It is still seem to me like a workaround, but if it applies to X3 and fixes the same issue I am ok with doing that.  
  
> ```  
> #include <boost/spirit/home/x3.hpp>  
>   
> int main(int argc, char** argv)  
> {  
>     char ch = '\xE3';  
>     parse(&ch, &ch + 1, boost::spirit::x3::digit);  
> }  
> ```  
  
> I do not think it is supported. How `std::isdigit` can work with multibyte encodings?  
  
C17 standard says:  
> The only functions in 7.4 whose behavior is not affected by the current locale are `isdigit` and `isxdigit`  
  
I still do not know if the standard allows passing non-ASCII codepoints with default C locale. I would appreciate a quote.
