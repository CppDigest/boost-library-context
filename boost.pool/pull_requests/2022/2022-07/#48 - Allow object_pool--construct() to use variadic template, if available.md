# #48 Allow object_pool::construct() to use variadic template, if available. [Closed]

> Username: fschoenberger  
> Created at: 2022-07-12 21:53:30 UTC  
> Updated at: 2025-07-03 16:55:53 UTC  
> Closed at: 2025-07-03 16:55:52 UTC  
> Url: https://github.com/boostorg/pool/pull/48  

As @jbherdman suggested I'm opening a new pull request with all of their changes in one commit. Hopefully, we get an accurate Code Coverage Report that way.   
  
This PR doesn't contain any new code that wasn't already included in PR 31.  
  
Fixes issue https://github.com/boostorg/pool/issues/22, with a fallback to the existing code for compilers that don't support variadic-templates.

---

## Comment 1

> Username: fschoenberger  
> Created_at: 2022-10-11 23:21:49 UTC  
> Url: https://github.com/boostorg/pool/pull/48#issuecomment-1275395016  

@jeking3 Sorry to bug you, but do you see this PR as ready to merge? If no: What else do I need to do? I'm currently always copy&pasting it into my projects; would love to see it make its way into the official distribution :)

---

## Comment 2

> Username: jeking3  
> Created_at: 2025-07-03 16:55:52 UTC  
> Url: https://github.com/boostorg/pool/pull/48#issuecomment-3032942717  

I rebased @jbherdman's PR.

---
