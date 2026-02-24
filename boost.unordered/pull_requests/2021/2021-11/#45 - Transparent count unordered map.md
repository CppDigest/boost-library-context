# #45 Transparent count unordered map [Merged]

> Username: cmazakas  
> Created at: 2021-11-22 20:28:56 UTC  
> Updated at: 2021-11-24 15:13:40 UTC  
> Merged at: 2021-11-23 23:50:29 UTC  
> Closed at: 2021-11-23 23:50:29 UTC  
> Url: https://github.com/boostorg/unordered/pull/45  

Closes #44

---

## Comment 1

> Username: cmazakas  
> Created_at: 2021-11-23 16:16:18 UTC  
> Url: https://github.com/boostorg/unordered/pull/45#issuecomment-976788976  

@pdimov @glenfe   
  
I think this PR is largely good to go. I'm not 100% sure on if this test suite is exhaustive enough so if you can think of anything to add, I'd greatly appreciate it.

---

## Comment 2

> Username: cmazakas  
> Created_at: 2021-11-23 20:19:53 UTC  
> Url: https://github.com/boostorg/unordered/pull/45#issuecomment-977131451  

@pdimov   
  
The test has been cleaned up so that the `key`'s assignment operator no longer exists as it's not needed. We've also refactored 3/4 of the test cases into their own function and there's now a helper function to clear all the `static` counts we use.

---

## Comment 3

> Username: pdimov  
> Created_at: 2021-11-23 20:29:36 UTC  
> Url: https://github.com/boostorg/unordered/pull/45#issuecomment-977144833  

This is fine, but you might want to move the transparent test into its own function as well; both for symmetry and because you'll probably need to do that anyway when you add `unordered_multimap` to the test.

---

## Comment 4

> Username: cmazakas  
> Created_at: 2021-11-23 22:21:42 UTC  
> Url: https://github.com/boostorg/unordered/pull/45#issuecomment-977224221  

> This is fine, but you might want to move the transparent test into its own function as well; both for symmetry and because you'll probably need to do that anyway when you add `unordered_multimap` to the test.  
  
Noted and updated!

---
