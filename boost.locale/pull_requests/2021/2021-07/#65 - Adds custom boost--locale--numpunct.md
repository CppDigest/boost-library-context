# #65 Adds custom boost::locale::numpunct [Open]

> Username: salvoilmiosi  
> Created at: 2021-07-04 13:50:28 UTC  
> Updated at: 2023-06-29 15:20:46 UTC  
> Url: https://github.com/boostorg/locale/pull/65  

#64 This PR attempts to fix the issue with the suggestion I've been making  
It removes the std::numpunct implementation and instead replaces it with a simple, custom class with a similar interface that returns string_type instead of CharType.

---

## Comment 1

> Username: artyom-beilis  
> Created_at: 2021-07-04 15:28:54 UTC  
> Url: https://github.com/boostorg/locale/pull/65#issuecomment-873611916  

Unfortunately it neither fixes the problem but also introduces new problems.  
  
Lets start from what it broke. boost.locale uses `std::numpunct` facet to format numbers for different locales. It gives partial solution (since it can't provide NBSP or custom digits etc) but at least gives something. Once you removed it `as::number` wouldn't work in std/posix/winapi backends.  
  
Additionally note thousands and decimal separator aren't enough - there is grouping - how to format it isn't always groups of three, sometimes it is two or other for example "10,00,00,00,00,000". So you need to extract both grouping and marks. But in ICU formatting of groups is very different from std::locale/posix locale. In you case if grouping returns "" - than no thousands separators are used.  
  
I recommend you to study the subject deeply before you write such a patch - in both terms localization and how it is done and how it is implemented in Boost.Locale. You can always ask questions

---

## Comment 2

> Username: salvoilmiosi  
> Created_at: 2021-07-04 15:56:03 UTC  
> Url: https://github.com/boostorg/locale/pull/65#issuecomment-873616180  

I must admit that my patch was a bit rushed. I have also now added grouping info to my icu implementation.  
I think we could add a std::numpunct facet by just pulling the first character from the string output and defaulting them to ', .' if they're more than one byte, just like it did in win32 and posix implementation.

---

## Comment 3

> Username: artyom-beilis  
> Created_at: 2021-07-04 15:59:30 UTC  
> Url: https://github.com/boostorg/locale/pull/65#issuecomment-873616740  

It is little bit complicated than that.  
  
https://en.wikipedia.org/wiki/Decimal_separator#Examples_of_use  
  
I really suggest first define what are you trying to solve. Than look what exists in icu and multiple locales (usually you'll be surprised how euro-centric your thinking will be) and how to solve  it properly.  
  
It is highly complex topic usually not solved by such a simple manner... And I'm not going to merge half baked solutions.

---

## Comment 4

> Username: salvoilmiosi  
> Created_at: 2021-07-04 17:04:11 UTC  
> Url: https://github.com/boostorg/locale/pull/65#issuecomment-873626428  

I have made my boost::locale::numpunct a subclass of std::numpunct to un-break winapi. Now this solves my problem of being able to access decimal_point, thousands_sep and grouping from icu backend and is compatible with all api's. I have pretty much just used the same technique that have already been in place for posix and winapi.

---

## Comment 5

> Username: artyom-beilis  
> Created_at: 2021-07-04 18:31:05 UTC  
> Updated_at: 2021-07-04 18:32:25 UTC  
> Url: https://github.com/boostorg/locale/pull/65#issuecomment-873638390  

> I have made my boost::locale::numpunct a subclass of std::numpunct to un-break winapi. Now this solves my problem of being able to access decimal_point, thousands_sep and grouping from icu backend and is compatible with all api's. I have pretty much just used the same technique that have already been in place for posix and winapi.  
  
But it does not solve the issue: `std::numpunct` should return "C" locale separators for std/posix/winapi backend as it is done in ICU. What you suggest is something entirely different.  
  
And I don't think it is correct thing to do.

---

## Comment 6

> Username: salvoilmiosi  
> Created_at: 2021-07-04 18:44:44 UTC  
> Updated_at: 2021-07-04 18:47:44 UTC  
> Url: https://github.com/boostorg/locale/pull/65#issuecomment-873640264  

So you're telling me that the current std::numpunct implementation in winapi and posix is broken but it's necessary because it's called by libstc++ in order for number formatting and parsing to work? Also why should numpunct always return "C" locale separators? Can't you just imbue std::locale::classic() if you want that functionality?

---

## Comment 7

> Username: artyom-beilis  
> Created_at: 2021-07-04 18:47:03 UTC  
> Url: https://github.com/boostorg/locale/pull/65#issuecomment-873640584  

> So you're telling me that the current std::numpunct implementation in winapi and posix is broken but it's necessary because it's called by libstc++ in order for number formatting and parsing to work?  
  
Exactly.

---

## Comment 8

> Username: artyom-beilis  
> Created_at: 2021-07-04 18:49:41 UTC  
> Url: https://github.com/boostorg/locale/pull/65#issuecomment-873640985  

> Also why should numpunct always return "C" locale separators? Can't you just imbue std::locale::classic() if you want that functionality?  
  
Because by default the formatting (without specific flags) by boost.locale is `std::locale::classic()` formatting

---

## Comment 9

> Username: salvoilmiosi  
> Created_at: 2021-07-04 18:51:23 UTC  
> Url: https://github.com/boostorg/locale/pull/65#issuecomment-873641211  

I guess I'll live with my fork in my own project then.

---

## Comment 10

> Username: Flamefire  
> Created_at: 2021-07-06 06:53:57 UTC  
> Url: https://github.com/boostorg/locale/pull/65#issuecomment-874507876  

I think it all boils down to this:  
  
> I really suggest first define what are you trying to solve.   
  
See https://github.com/boostorg/locale/issues/64#issuecomment-873569005 for why it is working as defined for ICU and https://github.com/boostorg/locale/issues/64#issuecomment-873570242 for what is currently NOT working  
  
Patches and fixes are always great, but first the expected behavior needs to be defined and evaluated. Especially when it diverges from what was initially designed and reviewed(!)

---

## Comment 11

> Username: salvoilmiosi  
> Created_at: 2021-07-06 09:35:38 UTC  
> Url: https://github.com/boostorg/locale/pull/65#issuecomment-874612852  

All I need to do is to get decimal point, thousands separator and grouping because I need to define a regex to find and parse numbers from strings, and the library that I use to deal with numbers only supports std::numpunct. There is no way currently to get that information from boost::locale without hacks.

---

## Comment 12

> Username: Flamefire  
> Created_at: 2021-07-06 09:47:04 UTC  
> Url: https://github.com/boostorg/locale/pull/65#issuecomment-874619971  

So you need a std::numpunct for the output of `bl::as::number`? But that would be impossible as the interface cannot be fulfilled. See https://en.cppreference.com/w/cpp/locale/numpunct  
  
Let's take https://github.com/boostorg/locale/issues/64#issuecomment-873563986  
> If you want to format number it is also wrong to use facet because number formatting is much more than just separator for example ١٢ is very valid and good number.  
  
If that is a possible output of `bl::as::number` then the whole point of numpunct is mood. How would your library deal with that?  
  
-> Don't use bl::as::number, fix the posix numpunct in Boost.Locale and use that.

---

## Comment 13

> Username: bialasjaroslaw  
> Created_at: 2022-07-24 11:00:06 UTC  
> Updated_at: 2022-07-24 11:14:58 UTC  
> Url: https://github.com/boostorg/locale/pull/65#issuecomment-1193293929  

It is long. TL;DR -> skip 3 paragraphs  
  
I came here because I wanted to have a locale aware formatting functionality, that does not force me to use any heavy commercial product (if there is anything else that can work cross-platform I will be happy to try it).  
  
How hard it could be, right? There is a `std::locale`, so that is all, done. Except, it is not working if locale is not generated on the OS (exception is thrown to give ma a clear indication that I am screwed). That's not an option, are there any alternatives? `Boost.Locale` for the rescue. I checked difference between 3 backends: `std`, `posix` and `icu` (working on Linux). Looks fine, as it is enough to use `boost::generator` to generate a locale and imbue it on `ostream`. Although only `icu` backend works for locales that aren't generated for current OS, it is fine for me.  
  
Finally I can use generated locale and inject it to formatting library to have `double` an `int` localized - not so fast. `FMT` (which I am using) does not care about locale I am injecting. Why? Because it is using numpunct facet methods (like `grouping` and `thousands_sep`) and those are broken because numpunct is broken.  
  
#### TL;DR  
  
To sum up. I can not even have `' '` for thousand separator, `','` for decimal separator and grouping size of 3, because there is NBSP - `C2 A0`, instead of `' '` in a locale that I want to support. There is no easy alternative provided, because (as far as I understand) no solution is better option, than broken (but close enough and maybe for some people acceptable) solution.  
  
#### Workaround  
  
I can always use `stringstream` and `boost::locale::as::number` to get similar results, but it will also require to change some code that I already have in my codebase with locale object injected to 3rd party library. I can at least try to apply some patching for FMT in case a different facet can be used with correct separators, but I can see none in the documentation except one that is mentioned in this toppic.   
One can also said that because I only need 3 simple pieces of data for each locale, it will be simpler to write a script that will generate that for my code.

---

## Comment 14

> Username: Flamefire  
> Created_at: 2022-07-24 15:23:18 UTC  
> Url: https://github.com/boostorg/locale/pull/65#issuecomment-1193339104  

I still don't understand the issue that is trying to be solved here.  
  
> I need to define a regex to find and parse numbers from strings, and the library that I use to deal with numbers only supports std::numpunct  
  
`std::numpunct` does not support multiple bytes for the separators, so returning the correct values for the selected locale is impossible.  
  
> There is no easy alternative provided, because (as far as I understand) no solution is better option, than broken (but close enough and maybe for some people acceptable) solution.  
  
So the issue is that the current `std::numpunct` implementation used by Boost.Locale should fall back to the values of the C locale  if the separators would be multi-byte to provide a "best-effort solution" for those cases, correct?  
  
Can you show a few simple test codes and expected outputs?

---

## Comment 15

> Username: bialasjaroslaw  
> Created_at: 2022-07-24 18:13:06 UTC  
> Url: https://github.com/boostorg/locale/pull/65#issuecomment-1193368281  

I have put some code in the repository on [GitHub](https://github.com/bialasjaroslaw/locale)  
I have used couple options to show what is going on with std, icu and posix backends for 3 different locales and couple ways of formating output (fmt and stringstream). I have also add a possibility to check how Qt behaves with same locales.  
  
It is worth mentioning what locales I have on my OS:  
  
> $ locale -a  
C  
C.UTF-8  
en_US.utf8  
pl_PL.utf8  
POSIX  
  
Question is: Should different backends behave differently in the same conditions?   
Right now ICU locale for FMT is always behaving like there is only C locale available, even if there is only single character group delimiter. Backends like std and posix are doing that only for locales not available in the OS, for others they provide "best-effort solution" (empty char instead of nbsp for pl_PL). In case of en_US it is correct delimiter as there is only single character.  
  
Stream based formating works well, but only for ICU, as std and posix provide only "best-effort" and produce the same output as in case of FMT format, so C for missing locales, and "best-effort" for multi character ones.  
  
QtLocale formatting is using ICU under the hood. As you can see numbers are formatted correctly in all cases.  
  
**To sum up.**   
I don't know if there should be "reasonable" replacement in case of multi-character separators or just fallback to C locale. I want to have one because it is convenient, but that might be not good enough (there might be some projects that rely on that behavior). On the other hand there is no easy way of formatting int/double values correctly for multiple locales except for ICU + streams, which is not always a possibility (like libfmt).  
  
There should be easily accessible and correct solution for a community. It was not "so easy" to write a code to inherit from `std::numpunct` to get at least "best-effort" results, and believe me I was determined.  
For now I can see couple options:  
* modify ICU backend and return "best-effort" separator. Problem is if it should be coherent with std and posix and return empty separator for nbsp or ' '. Other issue is some people may depend on current "broken" solution, but I don't know if it is a big deal  
* provide a header with custom facet that can be used to obtain separators, grouping and delimiters but as a correct type. Maybe there is already one somewhere, but I was not able to find it.  
* Force C++ standardization group to fix `std::numpunct` facet - how long could it take, and how hard can it be, right?

---

## Comment 16

> Username: Flamefire  
> Created_at: 2022-07-25 08:11:27 UTC  
> Url: https://github.com/boostorg/locale/pull/65#issuecomment-1193726932  

Ok, I'll tackle that soonish although I can't provide estimates. First I want to get the tests of this lib back passing to avoid introducing regressions.  
  
> Question is: Should different backends behave differently in the same conditions?  
  
Yes. IMO (I didn't invent this library, I only help maintaining) each backend should do formatting as good as possible. ICU is basically the "gold standard" in this regards so other backends may differ from ICU due to lack of the data tables etc. that ICU provides.  
  
> provide a header with custom facet that can be used to obtain separators, grouping and delimiters but as a correct type.   
  
So basically the approach done by this PR.  
  
So in summary this would be the solution:  
  
- provide a boost::locale::numpunct for all backends which for non-ICU backends behaves like the current `std::numpunct` solution (i.e. no change except having a facet in the Boost namespace which exists for all backends)  
- Enhance the facet to return strings via additional functions. For non-ICU this returns 1-char strings, for ICU it returns the correctly encoded string  
- For the ICU backend fall back to the C locale separator when it would be multi-char  
  
Hence power users could do:  
  
-  `std::use_facet<boost::locale::numpunct<char>>(loc).decimal_point()`  
-  `std::use_facet<boost::locale::numpunct<char>>(loc).decimal_point_str()`  
  
and other libs can always do `std::use_facet<std::numpunct<char>>(loc).decimal_point()` and get the same result as `std::use_facet<boost::locale::numpunct<char>>(loc).decimal_point()`  
  
This addresses all 3 points of your options:  
- best-effort result also for ICU  
- custom facet with string-type separators  
- something which can be added to the standard if sufficient and widely used and someone has the time and energy to write a paper and propose it for the standard

---

## Comment 17

> Username: Flamefire  
> Created_at: 2023-05-14 10:06:04 UTC  
> Url: https://github.com/boostorg/locale/pull/65#issuecomment-1546861538  

FYI: I'm working on this and will open a new PR containing your change, rebased to develop with conflicts resolved already. That was non-trivial so I didn't want to burden you with that especially as it took that long till I got to this.  
  
I'll also add tests for this which was a lot harder earlier but now should be pretty straight forward. So nothing do to from your side.  
  
Anyway: Thanks for your proposal and work and sorry for not getting to this earlier.

---
