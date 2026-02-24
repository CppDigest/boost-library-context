# #594 Replace invalid UTF-8 with valid UTF-8. [Closed]

> Username: rubenvb  
> Created at: 2020-05-04 14:08:44 UTC  
> Updated at: 2020-05-04 16:03:02 UTC  
> Closed at: 2020-05-04 16:03:02 UTC  
> Url: https://github.com/boostorg/spirit/pull/594  

This should fix issue #588.  
  
I checked all of `include/` for invalid UTF-8 by running this command from the terminal when my locale was set to a UTF-8 locale:  
```  
grep -Raxv '.*' include/  
```

---

## Comment 1

> Username: Kojoley  
> Created_at: 2020-05-04 15:15:02 UTC  
> Url: https://github.com/boostorg/spirit/pull/594#issuecomment-623524747  

We cannot do this either because source file character mapping is implementation specific http://www.eel.is/c++draft/lex.phases#1.1  
I am also afraid of altering copyright headers because I am not a lawyer.

---

## Comment 2

> Username: rubenvb  
> Created_at: 2020-05-04 15:28:52 UTC  
> Url: https://github.com/boostorg/spirit/pull/594#issuecomment-623533153  

I don't quite understand why your C++ Standard quote has any relevance. They were implementation-specific bytes before, they are still now. The difference is now they are in a known, very common encoding. If you can find one supported compiler that chokes on this change (and didn't on the unchanged code), I will eat my shoe.  
  
I can somewhat understand your copyright altering apprehension, if there was any altering going on. As it stands, this is just an encoding change. In the same sense you would be afraid to check out the Boost source code on Windows as you would alter line endings in the copyright header. If you look at the commit diff in any decent text viewer, you will see your font glyph picking can't even discern between before and after.  
  
Maybe @K-ballo him/her/...self can weigh in on the correct spelling of his/her/... name?

---
