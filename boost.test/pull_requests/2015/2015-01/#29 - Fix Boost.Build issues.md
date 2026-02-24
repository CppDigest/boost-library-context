# #29 Fix Boost.Build issues [Closed]

> Username: jhunold  
> Created at: 2015-01-10 13:36:46 UTC  
> Updated at: 2015-01-10 20:19:54 UTC  
> Closed at: 2015-01-10 20:19:54 UTC  
> Url: https://github.com/boostorg/test/pull/29  

Relevant commit is e2fd5e8  
  
I've removed the explicit linking to Boost.System and Boost.Timer as both dependencies are handled by Boost.Build automagically.  The test basic_cstring_test  
         [ test-btl-lib run : basic_cstring_test    : boost_unit_test_framework/<link>static ]  
 requires static runtime. Unconditionally adding both libraries globally (for all tests) results in linking against both shared and static library.  
And you only need <use> instead of <library> to draw header dependencies.

---

## Comment 1

> Username: raffienficiaud  
> Created_at: 2015-01-10 13:57:00 UTC  
> Url: https://github.com/boostorg/test/pull/29#issuecomment-69456587  

Sorry, I do not get it. Why are you closing the pull request?

---

## Comment 2

> Username: jhunold  
> Created_at: 2015-01-10 14:41:12 UTC  
> Url: https://github.com/boostorg/test/pull/29#issuecomment-69458025  

I wanted a clean patch against the original pull request. See https://github.com/ja11sop/test/pull/1 for this. Still experimenting with GitHub.

---

## Comment 3

> Username: raffienficiaud  
> Created_at: 2015-01-10 20:19:51 UTC  
> Url: https://github.com/boostorg/test/pull/29#issuecomment-69470352  

Since I did not understand exactly what happened, I merged the result of the merge of ja11sop#1 instead into the branch `tractickets/7397`.  
  
I now have a problem with the header only variant of the unit test. However we can close this PR.  
  
Thanks,  
Raffi

---
