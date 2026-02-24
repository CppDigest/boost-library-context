# #64 Re-add Boost.Array where it is ODR-used [Closed]

> Username: 13steinj  
> Created at: 2023-12-31 04:55:00 UTC  
> Updated at: 2024-01-21 12:00:57 UTC  
> Closed at: 2024-01-21 12:00:56 UTC  
> Url: https://github.com/boostorg/lexical_cast/pull/64  

Determining the size of a type requires its definition; of which there is a "not-C++11-compliant" use and a C++11-agnostic use in `.../converter_lexical_streams.hpp`.  
  
This was mistakenly forward-declared as part of af5ce2a5fe3321e2849a5ce89f80267c9059995e.  
  
Specifically, `sizeof` usages here:  
  
* https://github.com/boostorg/lexical_cast/commit/af5ce2a5fe3321e2849a5ce89f80267c9059995e#diff-a0b72e72f56fd9c8d25429567b14e23ef6b31f5091f3377428b8d6661798983eR473-R478  
  
* https://github.com/boostorg/lexical_cast/commit/af5ce2a5fe3321e2849a5ce89f80267c9059995e#diff-a0b72e72f56fd9c8d25429567b14e23ef6b31f5091f3377428b8d6661798983eL684-R687  
  
In case I picked usages that aren't in develop's head (as ctrl-f'ing that file shows more usages than I expect), here's current usage as well:  
  
https://github.com/boostorg/lexical_cast/blob/af5ce2a5fe3321e2849a5ce89f80267c9059995e/include/boost/lexical_cast/detail/converter_lexical_streams.hpp#L446-L456  
  
https://github.com/boostorg/lexical_cast/blob/af5ce2a5fe3321e2849a5ce89f80267c9059995e/include/boost/lexical_cast/detail/converter_lexical_streams.hpp#L473-L485  
  
Edit: changed GitHub /blame/ -> /blob/ since GitHub renders /blob/ nicely in comments/PRs.  
  
Interestingly enough; in the "non-C++11-compliant" case the `ifndef` / function was not removed; so the right answer might be to remove that function to drop down to a single usage and then remove that usage or put it as part of some static-compile test instead of adding arrays back. I don't remember if a reinterpret cast is an odr-use.

---

## Comment 1

> Username: apolukhin  
> Created_at: 2024-01-21 11:27:27 UTC  
> Url: https://github.com/boostorg/lexical_cast/pull/64#issuecomment-1902599380  

Thanks for the PR and issue report!  
  
The whole reinterpret_casting of arrays has a bad smell. Rewriting that part of the library in https://github.com/boostorg/lexical_cast/pull/68

---

## Comment 2

> Username: apolukhin  
> Created_at: 2024-01-21 12:00:57 UTC  
> Url: https://github.com/boostorg/lexical_cast/pull/64#issuecomment-1902606689  

Fixed in https://github.com/boostorg/lexical_cast/commit/4bf37fb6ceb8623bb068f754429576230904f901  
  
Thanks again for the report!

---
