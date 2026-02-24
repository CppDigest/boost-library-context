# #94 - case insensitive on swedish characters are not working [Closed]

> Username: Sopor  
> Created at: 2019-11-29 13:16:48 UTC  
> Updated at: 2020-02-18 12:42:51 UTC  
> Closed at: 2019-11-30 23:08:33 UTC  
> Url: https://github.com/boostorg/regex/issues/94  

I'm involved in a project that use boost and i have notice something with regex that is not working as it should, but i don't know if the issue is in boost or in the program that use boost. I'm not a programmer myself so i need to ask.  
  
When using (?i) that make a regex case insensitive it will work, but not on Swedish characters as `å, ä, ö and Å, Ä, Ö`. It will think these are two different chars. Same thing if i use a dot `.` that should represent any char, but it won't do that on these six chars.  
  
So could this be an issue in boost?

---

## Comment 1

> Username: JohnAlt2  
> Created at: 2019-11-29 13:39:18 UTC  
> Updated at: 2019-11-29 13:49:40 UTC  
> Url: https://github.com/boostorg/regex/issues/94#issuecomment-559793127  

Unicode double encoding within the software sounds most likely, or similar.   
If "." is not working on them then check that your program or database is not miss-encoding or double encoding these non-ASCII characters. When this happens then your single "Ö" may be converted in to multiple single byte characters hence a single "." will not skip what you expect to be a single character.  
Test against other non-ASCII characters.  
Test detecting "XÖX" using the regex "X.*?X".   
  
Otherwise...  
Not an expert, but before suspecting Boost I would suspect the Unicode library being used with Boost RegEx and the configuration of it. AFAIK it is the Unicode library that mostly defines the properties and associations of characters.

---

## Comment 2

> Username: Sopor  
> Created at: 2019-11-29 14:30:17 UTC  
> Url: https://github.com/boostorg/regex/issues/94#issuecomment-559807734  

@JohnAlt2 If i do as you suggested with `XÖX` and `X.*?X`, it will be not a match.  
  
I tried with other non-ASCII chars and it will be the same, not a match.

---

## Comment 3

> Username: JohnAlt2  
> Created at: 2019-11-29 16:24:43 UTC  
> Url: https://github.com/boostorg/regex/issues/94#issuecomment-559837791  

Strange, though I can think of a possible cause why the test failed. Try data "XXXXXÖXXXXX" and regex "X[^X]+X". Also as sanity check with test data "XXXXXaXXXXX" to ensure test is at least working with ASCII chars.  
But you should get the idea, experiment to find the boundaries of the behaviour.  
Also you could try and get your software to output the loaded data as a binary dump of some sort and output any matched string.

---

## Comment 4

> Username: jzmaddock  
> Created at: 2019-11-29 17:59:30 UTC  
> Url: https://github.com/boostorg/regex/issues/94#issuecomment-559856105  

I think you need to take this back to the programmers that are integrating Boost into the application - Boost.Regex is perfectly capable of matching non-ASCII characters, but it does need to be configured correctly (is it using ICU?  Or win32_regex_traits?), and programmed correctly (what's the input text format?  Is regex seeing full UTF-32 code points, or UTF-8 partial characters?).  So I would need a lot more details about how the library is being used before I could offer a solution.

---

## Comment 5

> Username: Sopor  
> Created at: 2019-11-30 23:08:33 UTC  
> Updated at: 2019-11-30 23:08:56 UTC  
> Url: https://github.com/boostorg/regex/issues/94#issuecomment-560028550  

It seems to be `win32_regex_traits`, but i will close this because it is probably not boost fault. Thanks you all for the info.

---

## Comment 6

> Username: Sopor  
> Created at: 2020-02-18 11:32:27 UTC  
> Updated at: 2020-02-18 12:42:51 UTC  
> Url: https://github.com/boostorg/regex/issues/94#issuecomment-587417329  

It seems that you need two dots to match å, ä or ö because they are two chars put together. But why does it work when i test it on regex testers? RegexBuddy do support Boost 1.66-1.68 and to match any of the chars i only need one dot. Can someone confirm that boost need two dots to match å, ä and ö?

---

## Comment 7

> Username: JohnAlt2  
> Created at: 2020-02-18 12:00:44 UTC  
> Url: https://github.com/boostorg/regex/issues/94#issuecomment-587427089  

Ah! That might actually make sense.   
I believe that accented characters such as "Ä" can be encode as either a single character U+00C4 or as a base letter "A" U+0041 plus a combining character U+0308 (https://unicode-table.com/en/0308/) which is just the accent (two floating dots) without and letter. This is rendered in display as a single "Ä" character. This is something you should be able to test.  
I suspect just the act of copying or pasting may convert the two characters in to a single character for compatibility purposes.  
See https://en.wikipedia.org/wiki/Combining_character  
If this is the case you might want to look at what Unicode library is being used, what library configurations are being used to handle international characters or equivalences (an area of ignorance for me). Alternatively have some pre-processing done to convert these combining characters, though I suspect should not be needed.  
(Note: I am not an expert in this area, Unicode and localisation is just something I stumble against occasionally)
