# #745 Update Unicode tables [Merged]

> Username: 3dyd  
> Created at: 2022-11-28 12:29:43 UTC  
> Updated at: 2023-01-03 23:16:06 UTC  
> Merged at: 2023-01-03 23:16:06 UTC  
> Closed at: 2023-01-03 23:16:06 UTC  
> Url: https://github.com/boostorg/spirit/pull/745  

Fixes #744  
  
Old Unicode data files had this comment:  
```  
# It is ok to redistribute this file "solely for informational   
# purposes in the creation of products supporting the Unicode Standard".  
# We don't nee to add a Boost License to this file: boostinspect:nolicense.   
```  
  
Original terms of use no longer contain quoted text from this comment, so I did not modify new Unicode data files but just added `boost-no-inspect` file to ignore the entire directory with these files. Also added https://www.unicode.org/license.txt there as this seems to be necessary.

---

## Comment 1

> Username: djowel  
> Created_at: 2022-11-29 00:30:15 UTC  
> Url: https://github.com/boostorg/spirit/pull/745#issuecomment-1329919878  

I'll defer to @Kojoley. He's doing the heavy-lifting of spirit maintenance.

---

## Comment 2

> Username: Kojoley  
> Created_at: 2022-11-29 01:12:38 UTC  
> Url: https://github.com/boostorg/spirit/pull/745#issuecomment-1329948543  

I'm actually not informed on the subject. One time I though about updating these, but had never found time to dig into.  
  
@djowel is the script handles everything automatically? I see new blocks added, but I have no idea whether it really fixed #744 or not. and IIRC we don't have proper unicode tests so it is also unclear that everything ok after the update.  
  
Low priority thing: do we really need these .txt files in the repository? I guess anyone who wants to update tables will download them anyway, so it would be better to replace them with a download script.

---

## Comment 3

> Username: 3dyd  
> Created_at: 2022-11-29 05:49:35 UTC  
> Url: https://github.com/boostorg/spirit/pull/745#issuecomment-1330112703  

> I have no idea whether it really fixed https://github.com/boostorg/spirit/issues/744 or not.  
  
I believe they do. I had tests in place, which failed after switching from internal table to `x3::unicode::format` for symbols 0x604, 0x605, 0x61C, 0x8E2, 0x180E, 0x2066-0x2069. After regenerating tables I verified if they fix #744. All tests passed.

---

## Comment 4

> Username: djowel  
> Created_at: 2022-11-29 05:52:20 UTC  
> Url: https://github.com/boostorg/spirit/pull/745#issuecomment-1330114611  

> @djowel is the script handles everything automatically? I see new blocks added, but I have no idea whether it really fixed #744 or not. and IIRC we don't have proper unicode tests so it is also unclear that everything ok after the update.  
  
Yes, they do. Maybe @3dyd might be interested to contribute some tests ;-)  
  
> Low priority thing: do we really need these .txt files in the repository? I guess anyone who wants to update tables will download them anyway, so it would be better to replace them with a download script.  
  
Makes sense. I trust your judgment, @Kojoley

---

## Comment 5

> Username: Kojoley  
> Created_at: 2022-11-29 22:23:45 UTC  
> Url: https://github.com/boostorg/spirit/pull/745#issuecomment-1331397357  

It took me a while to connect a few things, and there is an issue. The create_tables.cpp is hardcoded to specific list of General Categories, Derived Properties and Scripts. I've checked and found that the first two didn't change (and only a subset is used in Spirit) but Scripts did change (there are additions and removals). IIUC these things are defined in PropertyValueAliases.txt which is not processed by create_tables.cpp and is not presented in the repository either.  
  
Manually processed data I was able to identify is in:  
  
- [create_tables.cpp](https://github.com/boostorg/spirit/blob/859fd9cf85e2be2ea284690ce914cb5af47159b3/workbench/unicode/create_tables.cpp#L191-L332)  
- boost/spirit/home/support/char_encoding/unicode/query.hpp   
- boost/spirit/home/support/char_encoding/unicode.hpp  
- boost/spirit/home/support/char_class.hpp  
- boost/spirit/home/support/common_terminals.hpp  
- boost/spirit/home/x3/char/unicode.hpp

---

## Comment 6

> Username: djowel  
> Created_at: 2022-11-30 00:22:55 UTC  
> Url: https://github.com/boostorg/spirit/pull/745#issuecomment-1331488567  

It's been a decade now (I think) since those were added. It's good to have someone have a fresh look at it. @3dyd it would be really nice if you can contribute some tests, esp. since you have a keen interest on spirit unicode support.

---

## Comment 7

> Username: 3dyd  
> Created_at: 2022-12-02 12:20:59 UTC  
> Url: https://github.com/boostorg/spirit/pull/745#issuecomment-1335156934  

@djowel, do you say about unit tests, e.g. one positive and one negative test for all main, sub, derived categories, and several scripts? Or some more comprehensive, but not automated testing, to be done just on my end?  
  
> I guess anyone who wants to update tables will download them anyway, so it would be better to replace them with a download script.  
  
@Kojoley, this still leaves manual work (compile and run `create_tables`, move generated header files, manually update scripts). Wouldn't it be enough then to just add "readme" file with complete description where to get data files and what to do with them? Or maybe go in opposite direction, i.e. not only download files, but also compile and run `create_tables` and move generated files to proper location?  
  
Would you be ok with Scripts updated manually? Or some sort of automation is expected here?

---

## Comment 8

> Username: djowel  
> Created_at: 2022-12-03 04:33:37 UTC  
> Url: https://github.com/boostorg/spirit/pull/745#issuecomment-1336049940  

> @djowel, do you say about unit tests, e.g. one positive and one negative test for all main, sub, derived categories, and several scripts? Or some more comprehensive, but not automated testing, to be done just on my end?  
>   
  
Let's start with something more basic. I think one positive and one negative test makes sense for now.

---

## Comment 9

> Username: 3dyd  
> Created_at: 2022-12-05 11:28:40 UTC  
> Url: https://github.com/boostorg/spirit/pull/745#issuecomment-1337176823  

Tests revealed two issues, both were fixed:  
  
- All code points not explicitly listed in `UnicodeData.txt` belong to general category "Unassigned". In generated table such code points were defaulted to zero, which stands for "Uppercase_Letter".  
  
- All code points not explicitly listed in `Scripts.txt` belong to script "Unknown". In generated table they were defaulted to zero, i.e. to first script in the list (this was Arabic).

---

## Comment 10

> Username: djowel  
> Created_at: 2023-01-03 16:33:47 UTC  
> Url: https://github.com/boostorg/spirit/pull/745#issuecomment-1369977522  

LGTM. Good to merge?

---
