# #209 Add support for <arch-and-model> in common.format-name [Merged]

> Username: pdimov  
> Created at: 2017-07-07 00:03:12 UTC  
> Updated at: 2021-10-02 21:00:09 UTC  
> Merged at: 2017-07-27 03:36:29 UTC  
> Closed at: 2017-07-27 03:36:29 UTC  
> Url: https://github.com/boostorg/build/pull/209  



---

## Review 1 [Commented]

> Username: stefanseefeld  
> Created_at: 2017-07-07 00:21:58 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/build/pull/209#pullrequestreview-48496693  

📁 src/tools/common.jam

```diff
1035 | 
1036 |+ # Create a tag for the architecture and model
1037 |+ # <architecture>x86 <address-model>32 would generate "x32"
```

> Username: stefanseefeld  
> Created_at: 2017-07-07 00:21:58 UTC  
> Url: https://github.com/boostorg/build/pull/209#discussion_r126047118  

So windows-on-intel would generate "x32" and "x64". What about ARM, PPC, etc. ? Do they generate "A32", "P64", etc. ? I'd rather Boost respects preexisting nomenclature for naming architectures, including "x86", "x86_64", "arm", "aarch64", etc.

> Username: pdimov  
> Created_at: 2017-07-07 00:29:19 UTC  
> Url: https://github.com/boostorg/build/pull/209#discussion_r126047707  

Yes, they generate `a32`, `p64`, and so on.


---

## Review 2 [Changes requested]

> Username: stefanseefeld  
> Created_at: 2017-07-07 04:44:10 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/build/pull/209#pullrequestreview-48518690  

It would be preferable to use established terminology for these architecture suffixes. Adding a simple indirection to map the below names ("x32", "a64", etc.) to existing architecture specs ("x86", "aarch64", etc.) should be easy...

---

## Comment 3

> Username: pdimov  
> Created_at: 2017-07-07 10:50:35 UTC  
> Updated_at: 2017-07-07 10:51:00 UTC  
> Url: https://github.com/boostorg/build/pull/209#issuecomment-313650286  

It would not be preferable to me. The current scheme reflects the underlying Boost.Build properties `<architecture>` and `<address-model>`, including the fact that they are independent. I do not view their obfuscation by replacement with well-established, but arbitrary and irregular, names, as an improvement.

---

## Comment 4

> Username: github-actions[bot]  
> Created_at: 2021-10-02 21:00:08 UTC  
> Url: https://github.com/boostorg/build/pull/209#issuecomment-932819749  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2 Please consider following up at https://github.com/bfgroup/b2/pulls

---
