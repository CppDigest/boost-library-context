# #177 Support \e and \E in character and string literals [Merged]

> Username: jefftrull  
> Created at: 2022-11-08 06:47:15 UTC  
> Updated at: 2022-11-09 02:08:50 UTC  
> Merged at: 2022-11-09 02:08:50 UTC  
> Closed at: 2022-11-09 02:08:50 UTC  
> Url: https://github.com/boostorg/wave/pull/177  

Updates the non-strict lexer to support e and E, both with value 27 (ASCII ESC).  
Also adds tests for lexing these literals, and checks that they are usable in conditional preprocessor expressions.  
  
If merged this will resolve #174

---

## Review 1 [Approved]

> Username: hkaiser  
> Created_at: 2022-11-08 12:33:38 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/wave/pull/177#pullrequestreview-1172038880  

LGTM, thanks!

---
