# #678 - Since boost 1.72 spirit::unicode::char_ fails to parse non-ASCII [Closed]

> Username: timo-schluessler  
> Created at: 2021-05-20 09:49:09 UTC  
> Updated at: 2025-05-09 11:53:54 UTC  
> Closed at: 2025-05-09 11:53:52 UTC  
> Url: https://github.com/boostorg/spirit/issues/678  

Sample code to reproduce the issue:  
  
```c++  
#define BOOST_SPIRIT_UNICODE  
#include <boost/spirit/include/qi.hpp>  
  
int main()  
{  
   typedef std::string::const_iterator iterator_type;  
   namespace qi = boost::spirit::qi;  
   namespace unicode = boost::spirit::unicode;  
  
   std::string input("\"Test ⏳\"");  
   qi::rule<iterator_type, std::string(), unicode::space_type> quoted_string = qi::lexeme['"' >> +(unicode::char_ - '"') >> '"'];  
  
   iterator_type iter = input.begin();  
   iterator_type end = input.end();  
   std::string output;  
   bool r = phrase_parse(iter, end, quoted_string, unicode::space, output);  
  
   if (r && iter == end)  
      std::cout << "successfully parsed " << input << " to " << output << std::endl;  
   else  
      std::cout << "failed to parse " << input << std::endl;  
  
   return 0;  
}  
```  
  
Thanks to [sehe](https://stackoverflow.com/a/67592419/121331) who bisected the issue down to commit 16159fb.  
Maybe this behavior is by intention - then I simply don't get the use and meaning of spirit::unicode and BOOST_SPIRIT_UNICODE.

---

## Comment 1

> Username: Kojoley  
> Created at: 2021-05-20 16:27:13 UTC  
> Url: https://github.com/boostorg/spirit/issues/678#issuecomment-845268585  

1. The code contains implementation-defined behavior ([\[lex/1.1\]](https://eel.is/c++draft/lex#phases-1.1)). Please rewrite it without unicode characters in the source code.  
2. Do you intentionally use `std::string` with unicode? That will encode it with the execution character set.  
3. '"' is not a unicode parser, probably it is the root cause of your issue.

---

## Comment 2

> Username: timo-schluessler  
> Created at: 2021-05-21 09:22:01 UTC  
> Updated at: 2021-05-21 09:42:51 UTC  
> Url: https://github.com/boostorg/spirit/issues/678#issuecomment-845814336  

1. Sorry I didn't know this was implementation-defined. In the real program the string is read in from a file which is encoded in UTF-8. Please find the updated example below.  
2. Yes. I would like to process the UTF-8 as plain 8-bit chars as the file format itself (say the control characters) is exclusively made up of ASCII characters, like the " in the example. The fields/values in the format though may contain any UTF-8 character. (And because of the way UTF-8 encodes unicode characters no single byte could be falsely interpreted as a valid ASCII control char.)  
3. Do you have an idea how to fix this?  
  
```c++  
#define BOOST_SPIRIT_UNICODE  
#include <boost/spirit/include/qi.hpp>  
  
int main()  
{  
   typedef std::string::const_iterator iterator_type;  
   namespace qi = boost::spirit::qi;  
   namespace unicode = boost::spirit::unicode;  
  
   std::string input("\"Test \xe2\x8f\xb3\"");  
   qi::rule<iterator_type, std::string(), unicode::space_type> quoted_string = qi::lexeme['"' >> +(unicode::char_ - '"') >> '"'];  
  
   iterator_type iter = input.begin();  
   iterator_type end = input.end();  
   std::string output;  
   bool r = phrase_parse(iter, end, quoted_string, unicode::space, output);  
  
   if (r && iter == end)  
      std::cout << "successfully parsed " << input << " to " << output << std::endl;  
   else  
      std::cout << "failed to parse " << input << std::endl;  
  
   return 0;  
}  
```  
**Edit:** Typo.

---

## Comment 3

> Username: Kojoley  
> Created at: 2021-05-21 16:18:10 UTC  
> Url: https://github.com/boostorg/spirit/issues/678#issuecomment-846076211  

> Yes. I would like to process the UTF-8 as plain 8-bit chars as the file format itself (say the control characters) is exclusively made up of ASCII characters, like the " in the example. The fields/values in the format though may contain any UTF-8 character. (And because of the way UTF-8 encodes unicode characters no single byte could be falsely interpreted as a valid ASCII control char.)  
  
This sounds as a duplicate of #675  
  
> Do you have an idea how to fix this?  
  
Use UTF-8 to UTF-32 conversion iterator, if it does not help try to replace `'"'` with `unicode::lit('"')`.

---

## Comment 4

> Username: 3dyd  
> Created at: 2021-05-23 11:20:25 UTC  
> Url: https://github.com/boostorg/spirit/issues/678#issuecomment-846546489  

I suppose this is not a duplicate. While the reason is basically the same (and it is well described [here](https://github.com/boostorg/spirit/blob/db8bdf3d718472149b9fdb7f56f8ab2e002748ed/include/boost/spirit/home/support/char_class.hpp#L31-L34)), mine is about character classification checks, and this one happens before that, when both Qi and X3 implicitly convert value of signed type to `boost::uint32_t` while calling `unicode::ischar`:  
https://github.com/boostorg/spirit/blob/db8bdf3d718472149b9fdb7f56f8ab2e002748ed/include/boost/spirit/home/support/char_encoding/unicode.hpp#L41-L46  
  
(0xE2 `char` becomes 0xFFFFFFE2 `uint32_t`)  
  
For example, `standard::ischar` accounts possibility of signed source values this way:  
https://github.com/boostorg/spirit/blob/db8bdf3d718472149b9fdb7f56f8ab2e002748ed/include/boost/spirit/home/support/char_encoding/standard.hpp#L36-L42  
  
@timo-schluessler you example would work if you use `standard` encoding instead of `unicode` (simply change `unicode` to `standard`).  
  
Also, as it appeared to be, my issue with `standard` encoding has already been fixed for Qi (6821c820). So, in boost 1.76+ (where this fix has landed) you can also use `qi::standard::alpha` and other character classification parses to match ASCII markup (assuming that default C locale is used).

---

## Comment 5

> Username: Kojoley  
> Created at: 2021-05-23 20:48:09 UTC  
> Url: https://github.com/boostorg/spirit/issues/678#issuecomment-846622462  

Mixing unicode with non-unicode parsers, using unicode parsers on non-unicode input, or non-unicode parsers on unicode input is not supported. I had been researching the problem and prototyping a solution (last time when reviewing #655/#649) but there are behavior choices with no 'one fits all'.

---

## Comment 6

> Username: timo-schluessler  
> Created at: 2021-05-25 08:20:57 UTC  
> Url: https://github.com/boostorg/spirit/issues/678#issuecomment-847660959  

Thanks for your replies. `unicode::lit()` does not exist but using `standard` instead of `unicode` fixes my issue. And I somewhat get the sense of it such that if the grammar only uses ASCII then I use `standard`. If the grammar would contain special characters then I would have to use `unicode` and also work with wide characters. Does that make sense?

---

## Comment 7

> Username: Trigve  
> Created at: 2021-06-04 08:16:06 UTC  
> Url: https://github.com/boostorg/spirit/issues/678#issuecomment-854471870  

I'm also hit by this while upgrading to boost 1.76.0.  
  
I'm still bit puzzled after reading documentation as there is minimum info about character encoding namespaces. What's the difference between `standard`, `standard_wide` and `unicode`?  
  
How I see it is that `standard` does use `char` and `standard_wide` uses `wchar_t`. But the string parsed could be in any encoding when using `standard`?

---

## Comment 8

> Username: Kojoley  
> Created at: 2021-06-04 14:20:17 UTC  
> Url: https://github.com/boostorg/spirit/issues/678#issuecomment-854764090  

> I'm still bit puzzled after reading documentation as there is minimum info about character encoding namespaces. What's the difference between `standard`, `standard_wide` and `unicode`?  
  
Obviously the difference is encoding.  
  
> How I see it is that `standard` does use `char` and `standard_wide` uses `wchar_t`. But the string parsed could be in any encoding when using `standard`?  
  
* `standard` uses standard classification functions from `ctype.h` header, which use global C locale, which defines the encoding (J.4 Locale-specific behavior).  
* `standard_wide` uses standard classification functions from `wctype.h` header, an encoding is implementation-defined unless `__STDC_ISO_10646__` macro is defined (J.3.4 Characters).  
* `unicode` is UTF-32.

---

## Comment 9

> Username: hhaoao  
> Created at: 2022-05-07 09:45:35 UTC  
> Url: https://github.com/boostorg/spirit/issues/678#issuecomment-1120177044  

How to print unicode _attr?  
  
```  
 error: invalid operands to binary expression ('std::ostream' (aka 'basic_ostream<char>') and 'std::vector<char32_t>')  
```  
  
```c++  
#define BOOST_SPIRIT_X3_UNICODE  
#include <boost/spirit/home/x3.hpp>  
  
auto f1 = [](auto& ctx){std::cout << _attr(ctx) << std::endl;}  
  
x3::rule<class tree, ast::tree> const tree = "tree";  
  
auto const tree_def =  
    lexeme[+(char_ -(eol))][f1]  
    >> int_  
    ;  
```

---

## Comment 10

> Username: tdauth  
> Created at: 2024-09-23 15:43:12 UTC  
> Url: https://github.com/boostorg/spirit/issues/678#issuecomment-2368682014  

Any updates on this issue? Will this be fixed or is there any workaround? I use code like this:  
  
  
```  
typedef std::istreambuf_iterator<byte> IteratorType;  
	typedef boost::spirit::multi_pass<IteratorType> ForwardIteratorType;  
  
	ForwardIteratorType first = boost::spirit::make_default_multi_pass(IteratorType(istream));  
	ForwardIteratorType last;  
  
	// used for backtracking and more detailed error output  
	namespace classic = boost::spirit::classic;  
	typedef classic::position_iterator2<ForwardIteratorType> PositionIteratorType;  
	PositionIteratorType position_begin(first, last);  
	PositionIteratorType position_end;  
  
	try  
	{  
		if (!client::parse(position_begin, position_end, this->sections()))  
		{  
			throw Exception(_("Parsing error."));  
		}  
	}  
  
  
  
...  
  
typedef char byte;  
typedef std::basic_istream<byte> InputStream;  
```  
Does this mean I have to change my char into some UTF8 type now to have a basic_istream for UTF8? While the design decision might make sense, it breaks older code.

---

## Comment 11

> Username: saki7  
> Created at: 2024-09-26 08:38:11 UTC  
> Url: https://github.com/boostorg/spirit/issues/678#issuecomment-2376313379  

@tdauth I would suggest porting your code to X3, and use the char-related parsers provided in the correct namespaces, like @Kojoley mentioned: <https://github.com/boostorg/spirit/issues/678#issuecomment-854764090>  
  
Also: (quoting from https://github.com/boostorg/spirit/issues/678#issuecomment-846622462)  
  
> Mixing unicode with non-unicode parsers, using unicode parsers on non-unicode input, or non-unicode parsers on unicode input is not supported.   
  
I feel that the problem on this Issue is thoroughly explained here, and I think that there's no actual issue in the Spirit's implementation.  
  
As a sidenote:  
- I'm using X3's char parsers from the unicode namespaces, alongside with `std::u32string::const_iterator`, and they're working fine without any encoding related issues.  
- I'm a native Japanese speaker who uses CJK (aka multibyte) characters on a daily basis, and I am pretty much familiar with the unicode-related implementation failures in western OSS. The unicode implementation of Spirit.X3 is concrete, and it can surely handle UTF-32 iterators.

---

## Comment 12

> Username: tdauth  
> Created at: 2024-10-02 12:59:24 UTC  
> Url: https://github.com/boostorg/spirit/issues/678#issuecomment-2388585510  

So for UTF-8 files I could simply use std::u8string and char8_t instead of std::string and char in Boost Spirit 2? I don't know yet how to migrate to X3, so I am looking for the easiest solution.

---

## Comment 13

> Username: saki7  
> Created at: 2024-10-03 15:36:40 UTC  
> Url: https://github.com/boostorg/spirit/issues/678#issuecomment-2391733604  

@tdauth Yes, you should pass unicode iterators to Spirit.  
Again, I would strongly recommend using X3, since Spirit.Qi is no longer actively maintained.  
Feel free to open a new issue with your specific code, if you have further questions.

---

## Comment 14

> Username: saki7  
> Created at: 2025-05-09 11:53:52 UTC  
> Url: https://github.com/boostorg/spirit/issues/678#issuecomment-2866260665  

Closing, as this is an invalid use case involving a mix of ASCII and Unicode characters.  
  
- reference: <https://tonsky.me/blog/unicode/>
