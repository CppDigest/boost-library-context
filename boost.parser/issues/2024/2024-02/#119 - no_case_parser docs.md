# #119 - no_case_parser docs [Closed]

> Username: tobias-loew  
> Created at: 2024-02-27 12:13:12 UTC  
> Updated at: 2024-03-01 00:15:13 UTC  
> Closed at: 2024-03-01 00:15:13 UTC  
> Url: https://github.com/boostorg/parser/issues/119  

The docs state that parsing will be done by _case-insensitive matching, based on Unicode case folding_, but it's not clear if there are restrictions on the texts to match: Do they need to be in some normalized case folded form? And, if so could you provide a function that transforms a string into that form.  
  
Btw. this also gave me headaches in the beginning with Spirit, as its no_case transforms the input into lowercase and then does case- _sensitive_ matches against the rules. So all texts to match have to be lowercase too.

---

## Comment 1

> Username: tobias-loew  
> Created at: 2024-02-27 16:14:28 UTC  
> Url: https://github.com/boostorg/parser/issues/119#issuecomment-1966920075  

found the description here:  
https://tzlaine.github.io/parser/doc/html/boost_parser__proposed_/tutorial/directives.html  
maybe a link to it from https://tzlaine.github.io/parser/doc/html/boost/parser/no_case_parser.html would be good  
  
But I'm still troubled (apart from finding name in the example🤣 ) on how no_case works:  
I've debugged through it and the mapping from "ß" to "ss" (lowercase) seems strange as "ß" is considered to be lowercase already.  
Of course, if we translate to uppercase, then "ß" maps to "SS" (there is no uppercase "ß")  
  
As far as I could figure out: Unicode lowercase fold of "ß" is still "ß" (source minaret.info/test/case.msp)  
  
Maybe there should be two no_case-directives: a lowercase and an uppercase folding version.  
  
Btw. the no_case example in the docs no longer compiles, as `as_utf32` moved to `details::text` namespace.

---

## Comment 2

> Username: tzlaine  
> Created at: 2024-02-28 04:56:50 UTC  
> Url: https://github.com/boostorg/parser/issues/119#issuecomment-1968230047  

There are upper- and lower-case code points for that character in Unicode.  From my tests:  
  
```c++  
constexpr auto capital_sharp_s = u8"ẞ"; // U+1E9E  
constexpr auto small_sharp_s = u8"ß";   // U+00DF  
```  
  
> As far as I could figure out: Unicode lowercase fold of "ß" is still "ß" (source minaret.info/test/case.msp)  
>   
> Maybe there should be two no_case-directives: a lowercase and an uppercase folding version.  
  
There's only one Unicode case folding algorithm.  It folds text to all-upper or all-lower case, depending on the script.  The per-script differences are due to some legacy/compatibility reason.  
  
In Parser, your input *and* the parser's text are case-folded, so it doesn't matter what you write for your parsers -- upper case, lower case, or a mix, are all fine.  For the same reason, it doesn't matter what casing your input has.  
  
> Btw. the no_case example in the docs no longer compiles, as as_utf32 moved to details::text namespace.  
  
For that example to work, you need to `#include <boost/parser/transcode_view.hpp>`.  I've added a note to that example, and to the part of the docs that talks about using `as_utfN` that mentions this.  
  
I think that addresses all your concerns, but I'll leave this open for a few days in case you think I missed something.

---

## Comment 3

> Username: tobias-loew  
> Created at: 2024-02-28 07:46:17 UTC  
> Url: https://github.com/boostorg/parser/issues/119#issuecomment-1968407515  

> For that example to work, you need to `#include <boost/parser/transcode_view.hpp>`. I've added a note to that example, and to the part of the docs that talks about using `as_utfN` that mentions this.  
  
I had the include in my code, but the problem is line 808-810 in transcode_view.hpp: here we're still in `namespace boost::parser::detail::text`. I'm not sure, but I guess you wanted those lines to be in `namespace boost::parser`?   
The same applies to lines 456-460.

---

## Comment 4

> Username: tobias-loew  
> Created at: 2024-02-28 07:56:57 UTC  
> Url: https://github.com/boostorg/parser/issues/119#issuecomment-1968420722  

> In Parser, your input _and_ the parser's text are case-folded, so it doesn't matter what you write for your parsers -- upper case, lower case, or a mix, are all fine. For the same reason, it doesn't matter what casing your input has.  
  
Great! This allows a rule to be used in case-sensitive and insensitive contexts!  
👍

---

## Comment 5

> Username: tobias-loew  
> Created at: 2024-02-28 08:43:05 UTC  
> Url: https://github.com/boostorg/parser/issues/119#issuecomment-1968489078  

> There are upper- and lower-case code points for that character in Unicode. From my tests:  
>   
> ```c++  
> constexpr auto capital_sharp_s = u8"ẞ"; // U+1E9E  
> constexpr auto small_sharp_s = u8"ß";   // U+00DF  
> ```  
>   
> > As far as I could figure out: Unicode lowercase fold of "ß" is still "ß" (source minaret.info/test/case.msp)  
> > Maybe there should be two no_case-directives: a lowercase and an uppercase folding version.  
>   
> There's only one Unicode case folding algorithm. It folds text to all-upper or all-lower case, depending on the script. The per-script differences are due to some legacy/compatibility reason.  
  
I found https://www.unicode.org/charts/case/ which seems to be the official reference for folding. That explains the behaviour.  
  
But to be frank, it's still weird to me that "Strasse" matches "Straße", since "ss" is different from "ß" (and both are lowercase) and it really makes a difference, e.g.  
- Buße (penance)   
- Busse (buses)  
  
"Er reinigte alle Busse, um Buße zu tun."  
"He cleansed all the buses to repent."  
  
Btw. Google-translate returned  
"He cleansed all the repentances to repent."   
(Google's AI seems to be keen on penance.)  
  
In my perception, no_case gives wrong results.  
Maybe instead of no_case there should be four (cf. https://www.unicode.org/charts/case/) different "use_case" directives   
  
- use_lower[]  
- use_title[]  
- use_upper[]  
- use_fold[]  
  
This would explicitly name the transformation used.

---

## Comment 6

> Username: tzlaine  
> Created at: 2024-02-29 00:21:28 UTC  
> Url: https://github.com/boostorg/parser/issues/119#issuecomment-1970155289  

Well, that's def. weird, the difference between those two words.  However, this is not a general-purpose string searching API.  The input might have either of those two words in it, but are you likely to write the other one in a parser?  If so, it's not much effort to add `string(...)` with the exact match that you want -- which would either be the case-folded version with the "ss", or the unfolded one if the parser does not appear inside of `no_case[]`.  The upshot is that natrual languages are hard, and even Unicode pretty much never produces perfect results that make sense in all cirumstances for a native speaker of a given language.  
  
I realoze `no_case[]` does not have "fold" in its name, but the docs clearly indicate that Unicode case folding is how it works.  I think that will have to be good enough.  I think that having the four cases you mentioned would be lead to more confusion than the status quo.  
  
One more thing -- you had asked previously about normalization, and I just realized I never answered that part.  Parser does not do any normalization.  If you want exact matches, it's up to you to put the input and all the parsers in the same normalization.  That being said, case folding is normalization-agnostic.  It produces the same folding regardless of the normalization form it is given.

---

## Comment 7

> Username: tobias-loew  
> Created at: 2024-02-29 08:45:14 UTC  
> Updated at: 2024-02-29 08:46:11 UTC  
> Url: https://github.com/boostorg/parser/issues/119#issuecomment-1970669662  

I agree. The situation arises from the interaction of Unicode, natural language and a letter which has weird properties wrt. case folding. (Luckily, I don't have to write parsers for natural language.)  
  
To come to an end: Doing case folding on both input and target is def. a win!  
Maybe something for the future: there's a lot of effort in the code to get `no_case` working, and in the end it's all about applying case folding. What about having a transformation-directive letting users plug-in their own transformations for input and target:  
  
`transform( _input_transformation_ )[ parser ]`  
`transform( _input_transformation_, _target_transformation_ )[ parser ]`  
  
I'm not totally sure, but that might come in handy, e.g. for parsing floats that use non-standard punctuation schemes (again, the Germans, we write 12.345,67 instead of 12'345.67)   
  
Any thoughts?

---

## Comment 8

> Username: tzlaine  
> Created at: 2024-03-01 00:12:55 UTC  
> Url: https://github.com/boostorg/parser/issues/119#issuecomment-1972189854  

> I agree. The situation arises from the interaction of Unicode, natural language and a letter which has weird properties wrt. case folding. (Luckily, I don't have to write parsers for natural language.)  
>   
> To come to an end: Doing case folding on both input and target is def. a win! Maybe something for the future: there's a lot of effort in the code to get `no_case` working, and in the end it's all about applying case folding. What about having a transformation-directive letting users plug-in their own transformations for input and target:  
>   
> `transform( _input_transformation_ )[ parser ]` `transform( _input_transformation_, _target_transformation_ )[ parser ]`  
  
This is interesting.  I think it's still loking for a solid use case, but I like it.  I'll open a separate ticket so this does not get lost.  
  
> I'm not totally sure, but that might come in handy, e.g. for parsing floats that use non-standard punctuation schemes (again, the Germans, we write 12.345,67 instead of 12'345.67)  
>   
> Any thoughts?  
  
Well, the implementation on master uses Spirit X3's number parsers, and those are not configurable.  On my dev branch, there are also implementations using `from_chars`, which is also non-configurable.  So, I don't think this is likely to happen.  If/when `std::scan` is available, I could use that, and use the optional locale.
