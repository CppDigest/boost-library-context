# #744 - x3::unicode::format does not recognize certain format control characters [Closed]

> Username: 3dyd  
> Created at: 2022-11-27 16:23:11 UTC  
> Updated at: 2023-01-03 23:16:07 UTC  
> Closed at: 2023-01-03 23:16:07 UTC  
> Url: https://github.com/boostorg/spirit/issues/744  

For example, this test would fail:  
```  
BOOST_TEST(boost::spirit::char_encoding::unicode::is_format(0x0604));  
```  
  
It seems that [UnicodeData.txt](https://github.com/boostorg/spirit/blob/develop/workbench/unicode/UnicodeData.txt) and other files in its directory are fairly old comparing to the ones at https://unicode.org/Public/UNIDATA/

---

## Comment 1

> Username: djowel  
> Created at: 2022-11-28 04:58:32 UTC  
> Url: https://github.com/boostorg/spirit/issues/744#issuecomment-1328539398  

Would you care submitting a PR for updating the files, and running the generator?

---

## Comment 2

> Username: otrempe  
> Created at: 2023-01-03 16:09:50 UTC  
> Url: https://github.com/boostorg/spirit/issues/744#issuecomment-1369950294  

While trying to make a parser using x3::unicode::other_symbol, I observed that characters that should be parsed as other_symbol are actually parsed as uppercase_letter.  
  
I don't know if it the same issue and if it should be fixed by https://github.com/boostorg/spirit/pull/745.  
  
Should I open a new issue for this?

---

## Comment 3

> Username: otrempe  
> Created at: 2023-01-03 16:14:29 UTC  
> Url: https://github.com/boostorg/spirit/issues/744#issuecomment-1369955656  

From https://github.com/boostorg/spirit/pull/745#issuecomment-1337176823,  
  
I conclude this is the same issue and the pull request should fix it
