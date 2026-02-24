# #165 Let expanded long integer literals participate in conditional expressions [Merged]

> Username: jefftrull  
> Created at: 2022-06-22 01:35:39 UTC  
> Updated at: 2022-06-22 18:23:07 UTC  
> Merged at: 2022-06-22 18:23:07 UTC  
> Closed at: 2022-06-22 18:23:07 UTC  
> Url: https://github.com/boostorg/wave/pull/165  

It seems that the grammar for conditional preprocessor expressions was not updated when long integer literals were added for C++11. This seems to work most of the time, as they get recognized as `PP_NUMBER` tokens, which are in the grammar. Sometimes, though, when they are produced as part of a macro expansion, they can appear as `LONGINTLIT` tokens, which are _not_ in the grammar. Wave then rejects the expression as invalid.  
  
These changes add `LONGINTLIT` tokens as valid numbers in expressions, and in doing so resolve #162

---

## Review 1 [Approved]

> Username: hkaiser  
> Created_at: 2022-06-22 13:26:41 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/wave/pull/165#pullrequestreview-1015175560  

LGTM, thanks!

---
