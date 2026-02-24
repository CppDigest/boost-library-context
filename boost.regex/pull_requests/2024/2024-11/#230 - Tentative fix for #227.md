# #230 Tentative fix for #227. [Merged]

> Username: jzmaddock  
> Created at: 2024-11-04 16:39:07 UTC  
> Updated at: 2024-11-08 17:23:02 UTC  
> Merged at: 2024-11-06 09:20:07 UTC  
> Closed at: 2024-11-06 09:20:07 UTC  
> Url: https://github.com/boostorg/regex/pull/230  



---

## Comment 1

> Username: cmazakas  
> Created_at: 2024-11-04 17:22:23 UTC  
> Updated_at: 2024-11-04 17:23:21 UTC  
> Url: https://github.com/boostorg/regex/pull/230#issuecomment-2455286675  

I'm not sure I understand the code anymore.  
  
If I do this locally:  
```cpp  
      else if(pstate->type == syntax_element_startmark)  
      {  
         match_startmark();  
         int idx = static_cast<const re_brace*>(pstate)->index;  
         pstate = pstate->next.p;  
         skip_until_paren(idx, false);  
         continue;  
      }  
```  
I'll see tons of test failures, kind of like this:  
```  
  libs/regex/test/regress/test_regex_search.hpp:67:Error in end location of sub-expression 3, found 2, expected -1. (While testing ICU interfaces)  
  libs/regex/test/regress/test_regex_search.hpp:60:Error in start location of sub-expression 3, found 2, expected -1. (While testing ICU interfaces)  
  libs/regex/test/regress/test_regex_search.hpp:67:Error in end location of sub-expression 3, found 2, expected -1. (While testing ICU interfaces)  
  libs/regex/test/regress/test_regex_search.hpp:60:Error in start location of sub-expression 3, found 2, expected -1. (While testing ICU interfaces)  
  libs/regex/test/regress/test_regex_search.hpp:67:Error in end location of sub-expression 3, found 2, expected -1. (While testing ICU interfaces)  
  libs/regex/test/regress/test_regex_search.hpp:60:Error in start location of sub-expression 3, found 2, expected -1. (While testing ICU interfaces)  
  libs/regex/test/regress/test_regex_search.hpp:67:Error in end location of sub-expression 3, found 2, expected -1. (While testing ICU interfaces)  
  libs/regex/test/regress/test_regex_search.hpp:60:Error in start location of sub-expression 3, found 2, expected -1. (While testing ICU interfaces)  
  libs/regex/test/regress/test_regex_search.hpp:67:Error in end location of sub-expression 3, found 2, expected -1. (While testing ICU interfaces)  
  libs/regex/test/regress/test_regex_search.hpp:60:Error in start location of sub-expression 3, found 2, expected -1. (While testing ICU interfaces)  
  libs/regex/test/regress/test_regex_search.hpp:67:Error in end location of sub-expression 3, found 2, expected -1. (While testing ICU interfaces)  
  libs/regex/test/regress/test_regex_search.hpp:60:Error in start location of sub-expression 3, found 2, expected -1. (While testing ICU interfaces)  
  libs/regex/test/regress/test_regex_search.hpp:67:Error in end location of sub-expression 3, found 2, expected -1. (While testing ICU interfaces)  
  
EXIT STATUS: 44  
```  
  
but if I do the same change you have locally, everything passes.  
  
It seems like it's caused by this line:  
```cpp  
         int idx = static_cast<const re_brace*>(pstate)->index;  
```  
  
I assumed this line had no side-effects. I didn't think to reorder the `match_startmark()` call. Any insights as to what's going on here?  
  
Edit:  
  
I typed my comment and then I understood why it passes vs fails.  
  
It's because `match_startmark()` mutates the state `idx`, in this case, we cache it first, mutate it and then we feed it into the subsequent function call. Okay, everything makes sense to me now.

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2024-11-04 18:19:43 UTC  
> Url: https://github.com/boostorg/regex/pull/230#issuecomment-2455404570  

> It's because match_startmark() mutates the state idx, in this case, we cache it first, mutate it and then we feed it into the subsequent function call. Okay, everything makes sense to me now.  
  
Nearly: it updates the state machine (pstate) to the next state in the machine so idex gets read from a state that is NOT a startmark (ie invalid memory in this context), then the subsequent `pstate = pstate->next.p;` moves it forward again which is a bug in this fix.  I've restricted the fix to `idx > 0` which is the case that causes the original bug (ie subexpression being marked as ended when it was never started).  Hopefully this will do for this case at least.  God this code is soooo old!

---

## Comment 3

> Username: cmazakas  
> Created_at: 2024-11-05 19:27:37 UTC  
> Url: https://github.com/boostorg/regex/pull/230#issuecomment-2457992153  

I can't claim too much understanding of the code... yet. But I think this is good to merge, unless you'd like me to add some more tests akin to what's happening here.  
  
We can really stress the state machine generation further, if you'd like.  
  
Also, if you want, if you outline a plan of modernization of the codebase, I can tackle that too, John.

---

## Comment 4

> Username: jzmaddock  
> Created_at: 2024-11-08 11:19:23 UTC  
> Url: https://github.com/boostorg/regex/pull/230#issuecomment-2464457732  

> Also, if you want, if you outline a plan of modernization of the codebase, I can tackle that too, John.  
  
To be honest, regex is pretty much in maintenance only mode and has been for a while.  I removed a lot of C++03-isms a while back so that's something at least ;)  It's more that I just haven't looked at this code in quite a while, and I'll warn you, some the state machines generated with the later Perl extensions are just plain horrible to understand (this is at a conceptual level, quite separate from the however the code is written/implemented).  Maybe move on to the next issues, do you have access to the oss-fuzz.com issues list now?

---

## Comment 5

> Username: cmazakas  
> Created_at: 2024-11-08 17:06:38 UTC  
> Url: https://github.com/boostorg/regex/pull/230#issuecomment-2465298034  

Ha! It seems like I do now. Yay.  
  
I was thinking in terms of "modernization", we'd get regex to a place where I can use clangd with it. clangd has the unfortunate requirement that all files be self-contained, and Regex seems to rely a lot on transitive includes.  
  
I'd also like to port the test suite to CMake as well, so this way, devs who are interested in the project can pull it down and do all their dev on it using clangd and CMake (like me).

---

## Comment 6

> Username: jzmaddock  
> Created_at: 2024-11-08 17:23:01 UTC  
> Url: https://github.com/boostorg/regex/pull/230#issuecomment-2465343082  

>I was thinking in terms of "modernization", we'd get regex to a place where I can use clangd with it. clangd has the unfortunate requirement that all files be self-contained, and Regex seems to rely a lot on transitive includes.  
  
I'm not familiar with clangd, but if a bit of header reorganisation would help that's fine.  The header dependency tree should be reasonably linear IMO, but that's not to say that something hasn't crept in....  
  
>I'd also like to port the test suite to CMake as well, so this way, devs who are interested in the project can pull it down and do all their dev on it using clangd and CMake (like me).  
  
That's fine, but please bare in mind that b2 will remain the canonical way to run the tests for some time to come.  Plus I am genuinely completely clueless about cmake - I simply don't use it!  
  
The priorities would seem to be the "new" issues that are marked as "Vulnerability", there appear to be 3 of them: [42499910](https://issues.oss-fuzz.com/issues/42499910), [42497726](https://issues.oss-fuzz.com/issues/42497726) and [42533134](https://issues.oss-fuzz.com/issues/42533134).

---
