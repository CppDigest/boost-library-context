# #149 #148: icu_regex_traits::translate_nocase doesn't use case-folding [Merged]

> Username: Dani-Hub  
> Created at: 2021-08-08 16:32:10 UTC  
> Updated at: 2021-08-15 17:34:24 UTC  
> Merged at: 2021-08-15 17:34:24 UTC  
> Closed at: 2021-08-15 17:34:24 UTC  
> Url: https://github.com/boostorg/regex/pull/149  

In translate_nocase replace u_tolower by u_foldCase(c, U_FOLD_CASE_DEFAULT) suitable for single codepoint case folding

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2021-08-08 17:15:15 UTC  
> Url: https://github.com/boostorg/regex/pull/149#issuecomment-894827782  

CI started off.

---

## Comment 2

> Username: Dani-Hub  
> Created_at: 2021-08-08 17:50:22 UTC  
> Updated_at: 2021-08-08 17:50:48 UTC  
> Url: https://github.com/boostorg/regex/pull/149#issuecomment-894831663  

@jzmaddock Please let me know whether you would prefer to see extra unit tests for this change. I was considering to add a specific test of `translate_nocase` on code points where this change would cause a difference, for example from the Cherokee language (e.g. [U+13F2](https://www.compart.com/en/unicode/U+13F2 which casefolds to itself) but for these letters the lower-case forms have been added starting with Unicode 8.0. Would that be both reasonable and acceptable to add?  
If so, can you recommend to me which test file I should edit for this?

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2021-08-11 17:56:15 UTC  
> Url: https://github.com/boostorg/regex/pull/149#issuecomment-897031212  

Hmm, sorry for the delay in getting back to you, I guess a small self-contained test would be best under libs/regex/test/unicode.

---

## Comment 4

> Username: Dani-Hub  
> Created_at: 2021-08-11 18:23:46 UTC  
> Url: https://github.com/boostorg/regex/pull/149#issuecomment-897050642  

Thanks for the recommendation, I'll look into your test structure during the upcoming weekend, where I need to become familiar with the Jamfile.v2 format (I never used this file format before). And I realized that I can take advantage of the `U_UNICODE_VERSION` macro from `unicode/uchar.h` to get information about the supported Unicode version at compile time. This would allow me to make some of the tests conditionally available.

---

## Comment 5

> Username: Dani-Hub  
> Created_at: 2021-08-12 20:12:33 UTC  
> Url: https://github.com/boostorg/regex/pull/149#issuecomment-897936331  

@jzmaddock I'm sorry for asking again, but I need to ask you again to start the workflow. I hope I did got my Jamfile edits correctly...

---

## Comment 6

> Username: jzmaddock  
> Created_at: 2021-08-13 07:51:21 UTC  
> Url: https://github.com/boostorg/regex/pull/149#issuecomment-898261962  

>I'm sorry for asking again, but I need to ask you again to start the workflow.   
  
No problem, on it's way now.

---

## Comment 7

> Username: Dani-Hub  
> Created_at: 2021-08-13 08:16:34 UTC  
> Updated_at: 2021-08-13 08:19:50 UTC  
> Url: https://github.com/boostorg/regex/pull/149#issuecomment-898275953  

@jzmaddock All tests have passed! I hope they also did actually run, not only link? Are you happy with the test state or would you prefer to see additional tests, such as specific ones that perform concrete regex searches or matches? If so, can you provide me with some hints about your concrete preferences?

---

## Comment 8

> Username: jzmaddock  
> Created_at: 2021-08-15 17:34:19 UTC  
> Url: https://github.com/boostorg/regex/pull/149#issuecomment-899085839  

Looks good to me, merging.

---
