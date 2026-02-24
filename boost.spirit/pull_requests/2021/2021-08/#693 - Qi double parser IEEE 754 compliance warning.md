# #693 Qi double parser IEEE 754 compliance warning [Merged]

> Username: taam  
> Created at: 2021-08-02 14:30:20 UTC  
> Updated at: 2025-05-09 11:36:29 UTC  
> Merged at: 2025-05-09 11:36:12 UTC  
> Closed at: 2025-05-09 11:36:12 UTC  
> Url: https://github.com/boostorg/spirit/pull/693  

This commit adds a warning regarding the issue described in #668. However, I do not know if ...  
* ... a warning is appropriate,  
* ... the placement is ok,  
* ... the statement and wording are actually correct,  
* ... maybe `float_` and `long_double` are affected as well (probably?),  
* ... maybe this applies to `real_parser` in general.  
  
It's at least a first try. I was also thinking about a longer version, but I didn't want to speculate.

---

## Comment 1

> Username: Kojoley  
> Created_at: 2021-08-02 19:31:21 UTC  
> Url: https://github.com/boostorg/spirit/pull/693#issuecomment-891277980  

Does it really has something to do with IEEE 754 compliance? Unfortunately I do not have a copy of the paper, if the paper does not allow quoting at least provide a paragraph number.

---

## Comment 2

> Username: taam  
> Created_at: 2021-08-02 20:48:21 UTC  
> Url: https://github.com/boostorg/spirit/pull/693#issuecomment-891322314  

I'm do not know, but I was assuming so due to your comments to that issue. I do not have the paper either. Seems I did speculate after all, sorry. That's why I was not eager to get involved into something I have no clue about.  
  
Didn't find any hint searching the web so far, I try to look into other parser implementations if I have time.

---

## Comment 3

> Username: taam  
> Created_at: 2021-08-04 13:25:00 UTC  
> Url: https://github.com/boostorg/spirit/pull/693#issuecomment-892656175  

So far I could not find anything definitive, just hints that there is a single correct conversion, e.g. https://github.com/google/double-conversion/blob/44944accfd5516a094e1e730334764e0a908aaff/double-conversion/strtod.cc very much seems to assume that there is "the correct double".  
  
And maybe even better: https://github.com/fastfloat/fast_float/blame/main/README.md#L32-L34

---

## Comment 4

> Username: djowel  
> Created_at: 2021-08-05 00:55:42 UTC  
> Url: https://github.com/boostorg/spirit/pull/693#issuecomment-893079718  

If there are tests for conformance, that is probably what we should be looking for and add to our tests.   
  
I offered a way to fix it: promote to a higher precision when available and only when necessary (the if-else branch). It should handle float and double. If there's no way to promote (i.e. T already has the highest precision), a proper fix would have to convert it to a quad [Quadruple precision floating-point]. The promotion from lower precision should be a lower hanging fruit. I'll have a look if I can get some free time.

---

## Comment 5

> Username: saki7  
> Created_at: 2025-05-09 11:21:44 UTC  
> Url: https://github.com/boostorg/spirit/pull/693#issuecomment-2866180549  

Provided improved wording.

---

## Comment 6

> Username: saki7  
> Created_at: 2025-05-09 11:36:28 UTC  
> Url: https://github.com/boostorg/spirit/pull/693#issuecomment-2866218218  

Thanks for your contribution! While the bug still exists, adding a warning to the documentation is a valuable first step.  
Note that the bug itself is tracked on another issue, so this PR can be treated solely as a documentation fix.

---
