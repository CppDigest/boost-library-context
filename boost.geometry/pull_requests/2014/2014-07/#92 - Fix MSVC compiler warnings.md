# #92 Fix MSVC compiler warnings [Merged]

> Username: MarcelRaad  
> Created at: 2014-07-12 11:25:03 UTC  
> Updated at: 2014-07-25 15:36:43 UTC  
> Merged at: 2014-07-12 21:57:37 UTC  
> Closed at: 2014-07-12 21:57:37 UTC  
> Url: https://github.com/boostorg/geometry/pull/92  

This removes unused variables and redundant re-declarations to silence  
warnings about variable shadowing. Tested on MSVC 14 CTP2.

---

## Comment 1

> Username: awulkiew  
> Created_at: 2014-07-12 17:21:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/92#issuecomment-48818323  

Thanks for the testing and fixes!  
  
Wow, the new MSVC will probably generate even more interesting warnings...  
  
Some of the issues I'd handle differently, e.g.:  
- index - I'd probably create an additional block around the first loop and use the first index only there,  
- pig - I'd avoid reassigning so probably just change the names which would also better describe them and use them locally (also made them const but that's an another story),  
- i in pj_ell_set() - I'm not sure if we should declare the variables in conditions, are there other places in the Geometry where this is done? I'd probably replace this "if else" by "else" and inside put the "i" variable definition and additional if statement.  
  
In general I'm not against the merge but I'll wait with it in case someone was.

---

## Comment 2

> Username: barendgehrels  
> Created_at: 2014-07-12 18:04:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/92#issuecomment-48819440  

About pj_ell_set, this is ported code and AFAIR, as close as the original as possible (to make reporting more convenient). So in this case I'm OK with declaring inside the condition.  
  
Thanks for this PR.

---

## Comment 3

> Username: MarcelRaad  
> Created_at: 2014-07-13 05:30:35 UTC  
> Url: https://github.com/boostorg/geometry/pull/92#issuecomment-48832341  

Thanks for your comments! I agree that reducing the scope of variables, making them const and giving them better names would be better than re-assigning them, sorry I noticed your comments just now.  
Thanks for merging!

---

## Comment 4

> Username: awulkiew  
> Created_at: 2014-07-25 13:09:31 UTC  
> Url: https://github.com/boostorg/geometry/pull/92#issuecomment-50146130  

The fix for pj_ell_set doesn't compile in any of the tested compiler (http://www.boost.org/development/tests/develop/developer/geometry-extensions.html).  
Does it really work in MSVC2014?  
Anyway, I fixed it this way: https://github.com/boostorg/geometry/commit/c4e442fffedc2f660fd5a8f28c1eb9a0e1d50219  
Let me know if you think it should be fixed differently.

---

## Comment 5

> Username: MarcelRaad  
> Created_at: 2014-07-25 13:42:53 UTC  
> Url: https://github.com/boostorg/geometry/pull/92#issuecomment-50150537  

Oh, I'm very sorry! It compiled without the braces around the declaration, but then I noticed it did the wrong thing (assign the whole rest of the condition to i including the part after ||) and I must have compiled the wrong unit test after adding them :-( Thanks for the fix!

---
