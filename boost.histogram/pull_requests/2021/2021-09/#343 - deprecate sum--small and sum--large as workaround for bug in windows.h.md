# #343 deprecate sum::small and sum::large as workaround for bug in windows.h [Merged]

> Username: HDembinski  
> Created at: 2021-09-30 08:11:42 UTC  
> Updated at: 2021-09-30 16:41:24 UTC  
> Merged at: 2021-09-30 09:20:56 UTC  
> Closed at: 2021-09-30 09:20:57 UTC  
> Url: https://github.com/boostorg/histogram/pull/343  

Closes #342   
  
This is based on the discussion in #341, but without immediately breaking the API. Instead, the old API is deprecated in favor of an API that works around the issue.  
  
We should not have to work around Microsoft violating the C++ standard, but here we are.

---

## Comment 1

> Username: HDembinski  
> Created_at: 2021-09-30 08:29:56 UTC  
> Url: https://github.com/boostorg/histogram/pull/343#issuecomment-931030971  

@emmenlau FYI

---

## Comment 2

> Username: emmenlau  
> Created_at: 2021-09-30 09:58:46 UTC  
> Url: https://github.com/boostorg/histogram/pull/343#issuecomment-931174924  

Oh my, I'm so sorry it had to come to this! But its highly appreciated and will possibly help many users in the long run. Thanks a lot @HDembinski for all your effort!

---

## Comment 3

> Username: henryiii  
> Created_at: 2021-09-30 16:41:12 UTC  
> Updated_at: 2021-09-30 16:41:24 UTC  
> Url: https://github.com/boostorg/histogram/pull/343#issuecomment-931487086  

Good workaround for an unfortunate issue. In boost-histogram, should we move to using the new names or keep the old names (since they are just strings in pybind11, and Python obviously supports large and small)? The old names are shorter and easier to remember/use in the REPL, etc, but the longer names match Boost.Histogram better. (and the sum accumulator doesn't have much use except for teaching about accumulators or for doing a manual highly-accurate accumulation, where most users won't need the two parts). We could even provide the official names, but have the short names as non-deprecated shortcuts if you want.

---
