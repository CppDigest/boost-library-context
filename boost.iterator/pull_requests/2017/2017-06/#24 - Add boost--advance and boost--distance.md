# #24 Add boost::advance and boost::distance [Merged]

> Username: morinmorin  
> Created at: 2017-06-29 13:13:36 UTC  
> Updated at: 2017-07-17 21:35:58 UTC  
> Merged at: 2017-06-29 19:02:39 UTC  
> Closed at: 2017-06-29 19:02:39 UTC  
> Url: https://github.com/boostorg/iterator/pull/24  



---

## Comment 1

> Username: eldiener  
> Created_at: 2017-07-17 16:27:02 UTC  
> Url: https://github.com/boostorg/iterator/pull/24#issuecomment-315805483  

I merged this to 'master' for Boost 1.65. Maybe you can add to the Iterator docs something about using 'advance' and 'distance', as others will not know about it unless it has been documented.

---

## Comment 2

> Username: morinmorin  
> Created_at: 2017-07-17 21:35:58 UTC  
> Url: https://github.com/boostorg/iterator/pull/24#issuecomment-315892180  

> I merged this to 'master' for Boost 1.65.  
  
Nice, thanks!  
  
> Maybe you can add to the Iterator docs something about using 'advance' and 'distance', as others will not know about it unless it has been documented.  
  
I tried to update the documentation in this PR, but I found that the HTML docs have not been updated for years; then, I gave it up...  
  
What I thought about documentation update was:  
add the following to "Iterator Utilities" Section.  
#### Operations  
- `advance.hpp`: Provides the implementation of `advance` function for new-style iterators.  
- `distance.hpp`: Provides the implementation of `distance` function for new-style iterators.  
  
Is it OK to only update `iterator.qbk`?

---
