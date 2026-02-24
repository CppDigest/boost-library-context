# #54 Inheritance from std::iterator is deprecated in c++17. Therefore repl… [Merged]

> Username: DanielaE  
> Created at: 2017-12-18 18:56:38 UTC  
> Updated at: 2017-12-24 08:47:14 UTC  
> Merged at: 2017-12-23 19:35:28 UTC  
> Closed at: 2017-12-23 19:35:28 UTC  
> Url: https://github.com/boostorg/regex/pull/54  

Inheritance from std::iterator is deprecated in c++17. Therefore replace it by lifting it's members into the derived class. Fortunately, this is already done in Boost.Regex so that dropping the inheritance is a no-brainer.  
  
Signed-off-by: Daniela Engert <dani@ngrt.de>

---

## Review 1 [Commented]

> Username: pdimov  
> Created_at: 2017-12-22 00:04:25 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/regex/pull/54#pullrequestreview-85214339  

📁 include/boost/regex/v4/u32regex_iterator.hpp

```diff
  79 | public:
  80 |    typedef          u32regex                                                regex_type;
  89 |-    typedef          match_results<BidirectionalIterator>                    value_type;
```

> Username: pdimov  
> Created_at: 2017-12-22 00:04:25 UTC  
> Updated_at: 2017-12-22 15:10:02 UTC  
> Url: https://github.com/boostorg/regex/pull/54#discussion_r158405495  

`typename` is wrong here. Not sure why you decided to add it.

> Username: DanielaE  
> Created_at: 2017-12-22 06:43:25 UTC  
> Updated_at: 2017-12-22 15:10:02 UTC  
> Url: https://github.com/boostorg/regex/pull/54#discussion_r158436961  

I see ☹️ . My problem is that the very same test-cases failed without 'typename' on travis. Starting without that 'typename' I've got lengthy error messages about a missing 'value_type'. Reluctantly adding it (I saw no obvious reason for that) is giving much short error messages - well, at least some sort of improvement. Unfortunately I have no other means of running tests with gcc or clang on a sufficiently matching setup.

> Username: pdimov  
> Created_at: 2017-12-22 15:04:11 UTC  
> Updated_at: 2017-12-22 15:10:02 UTC  
> Url: https://github.com/boostorg/regex/pull/54#discussion_r158506965  

It's too convoluted to follow and I get no failures with mingw g++ 7, I guess John has to take a look at it. `match_results` is for some reason declared in `v4/regex_format.hpp:56`, which I can only suspect is not being included prior to this header, hence the errors. But it's hard to tell. Either way, I think that it's more correct without the `typename` than with it.

> Username: pdimov  
> Created_at: 2017-12-22 15:07:36 UTC  
> Updated_at: 2017-12-22 15:10:02 UTC  
> Url: https://github.com/boostorg/regex/pull/54#discussion_r158507595  

Ah, there are no errors because `icu.cpp` only includes `icu.hpp` when `BOOST_HAS_ICU` is defined and it isn't for me.

> Username: DanielaE  
> Created_at: 2017-12-22 15:13:32 UTC  
> Url: https://github.com/boostorg/regex/pull/54#discussion_r158508540  

I suspect that std::iterator_traits is implemented SFINAE-friendly, and the ```value_type``` member is no longer present then. I've reworked the patch such that it should no longer SFINAE-away those members now.

> Username: pdimov  
> Created_at: 2017-12-22 15:26:17 UTC  
> Url: https://github.com/boostorg/regex/pull/54#discussion_r158510541  

Installing libicu-devel under Cygwin reproduces the errors.

> Username: DanielaE  
> Created_at: 2017-12-22 15:35:07 UTC  
> Updated_at: 2017-12-22 15:35:08 UTC  
> Url: https://github.com/boostorg/regex/pull/54#discussion_r158512087  

BTW, it looks like Boost.Regex isn't ICU-enabled for all Windows test-runs in Boost's test-matrix. I had to spend quite some time to make this work here on my local Windows machine. Some tests for the ```wchar_t```-overloads were failing. It seems like there is an implicit assumption that ICU's ```UChar``` is the same as ```wchar_t```. But it's not: ```UChar``` is typedef'd to ```char16_t``` and that' *not* the same as ```wchar_t```.


---

## Comment 2

> Username: pdimov  
> Created_at: 2017-12-22 15:51:17 UTC  
> Url: https://github.com/boostorg/regex/pull/54#issuecomment-353623814  

With your revised patch, the only remaining failure under Cygwin gcc w/ ICU installed is  
  
```  
test\concepts\icu_concept_check.cpp: In function 'int main()':  
test\concepts\icu_concept_check.cpp:91:65: error: no matching function for call  
to 'u32regex_match(const wchar_t*, boost::match_results<const wchar_t*>&, boost:  
:u32regex&, boost::regex_constants::match_flag_type&)'  
    b = boost::u32regex_match((const wchar_t*)(pb), cm4, e1, flgs);  
                                                                 ^  
```  
  
which is probably caused - as you observe - by ICU using `char16_t` nowadays.

---

## Comment 3

> Username: DanielaE  
> Created_at: 2017-12-22 15:57:13 UTC  
> Url: https://github.com/boostorg/regex/pull/54#issuecomment-353625040  

I have a fix for that: https://github.com/DanielaE/boost.regex/commit/9b1f9cac005410c4a32844252c7d86d341560264 I'd appreciate a review 😄

---

## Comment 4

> Username: pdimov  
> Created_at: 2017-12-22 15:57:23 UTC  
> Url: https://github.com/boostorg/regex/pull/54#issuecomment-353625067  

To clarify, this same failure is present on `develop`, so it's independent of this patch.

---

## Comment 5

> Username: pdimov  
> Created_at: 2017-12-22 16:06:12 UTC  
> Url: https://github.com/boostorg/regex/pull/54#issuecomment-353627124  

Your fix is not quite correct, because the presence of `char16_t` does not guarantee that the installed ICU version will use it for `UChar`.  
  
This seems subtle and maybe the right way to approach it is via an issue and not a pull request. :-)  
  
I can think of a few ways to fix it but they aren't quite equivalent to the current code since it allows types with implicit conversions to `wchar_t const*` and a template would not.  
  
So maybe leave this to John.

---

## Comment 6

> Username: pdimov  
> Created_at: 2017-12-22 16:14:31 UTC  
> Url: https://github.com/boostorg/regex/pull/54#issuecomment-353628750  

Looking at `unicode/umachine.h`, the logic is:  
  
- on ICU version 59, `UChar` is always `char16_t` in C++ mode;  
- on ICU version 58 and below, `UChar` is  
```  
#if U_SIZEOF_WCHAR_T==2  
    typedef wchar_t OldUChar;  
#elif defined(__CHAR16_TYPE__)  
    typedef __CHAR16_TYPE__ OldUChar;  
#else  
    typedef uint16_t OldUChar;  
#endif  
```

---

## Comment 7

> Username: pdimov  
> Created_at: 2017-12-22 16:19:49 UTC  
> Url: https://github.com/boostorg/regex/pull/54#issuecomment-353629889  

Since we're only interested in whether `UChar` is `wchar_t`, this collapses to  
  
- on ICU version 59, never  
- on ICU version 58 and below, when `U_SIZEOF_WCHAR_T == 2`.  
  
That is, `UChar` is `wchar_t` when `U_ICU_VERSION_MAJOR_NUM < 59 && U_SIZEOF_WCHAR_T == 2`.

---

## Comment 8

> Username: pdimov  
> Created_at: 2017-12-22 16:23:35 UTC  
> Url: https://github.com/boostorg/regex/pull/54#issuecomment-353630607  

The additional check for `U_WCHAR_IS_UTF16` in `icu.hpp` does not seem to be necessary, if I go by the actual `UChar` definition, as this macro does not affect it.

---

## Comment 9

> Username: pdimov  
> Created_at: 2017-12-22 16:31:33 UTC  
> Url: https://github.com/boostorg/regex/pull/54#issuecomment-353632014  

In code, this would be something like  
  
```  
#define BOOST_REGEX_UCHAR_IS_WCHAR_T (U_ICU_VERSION_MAJOR_NUM < 59 && U_SIZEOF_WCHAR_T == 2)  
  
#if BOOST_REGEX_UCHAR_IS_WCHAR_T  
BOOST_STATIC_ASSERT((boost::is_same<UChar, wchar_t>::value));  
#else  
BOOST_STATIC_ASSERT(!(boost::is_same<UChar, wchar_t>::value));  
#endif  
  
// use BOOST_REGEX_UCHAR_IS_WCHAR_T for the overloads  
```

---

## Comment 10

> Username: DanielaE  
> Created_at: 2017-12-23 08:56:58 UTC  
> Url: https://github.com/boostorg/regex/pull/54#issuecomment-353715044  

I'm happy, my last change made all tests green except for XCode 6 and 7 where `bootstrap.sh` fails and no tests are run at all.

---

## Comment 11

> Username: jzmaddock  
> Created_at: 2017-12-23 19:41:14 UTC  
> Url: https://github.com/boostorg/regex/pull/54#issuecomment-353744375  

Thanks I'll deal with the UChar issues later (unless someone files a PR first!)

---

## Comment 12

> Username: pdimov  
> Created_at: 2017-12-23 23:52:03 UTC  
> Url: https://github.com/boostorg/regex/pull/54#issuecomment-353754907  

Created a PR implementing the above suggestion at https://github.com/boostorg/regex/pull/55.

---
