# #19 Fix to pull request #13  [Merged]

> Username: brunocodutra  
> Created at: 2015-03-22 16:55:07 UTC  
> Updated at: 2015-03-30 16:49:37 UTC  
> Merged at: 2015-03-22 18:25:07 UTC  
> Closed at: 2015-03-22 18:25:07 UTC  
> Url: https://github.com/boostorg/mpl/pull/19  

Avoiding compile errors on visual studio compilers. Errors were due to the way MPL_ASSERT is defined for these compilers.  
All tests were verified to pass on MSVC 2010.  
  
@mclow could you please merge this fix?

---

## Comment 1

> Username: brunocodutra  
> Created_at: 2015-03-27 16:19:34 UTC  
> Url: https://github.com/boostorg/mpl/pull/19#issuecomment-86993541  

@eldiener @mclow Just to let you guys know I've been watching the test runners and everything seems fine, I guess the fix is safe to be merged into master, so it makes it to 1.58.  
  
I also have two other open pull requests, #14 and #16, addressing three open Tickets, 7975, 8749 and 11001. Both are fairly straightforward and shouldn't demand too much time to be reviewed, perhaps you guys could take a look at those as well?  
  
Thank you for your help.

---

## Comment 2

> Username: mclow  
> Created_at: 2015-03-30 16:49:37 UTC  
> Url: https://github.com/boostorg/mpl/pull/19#issuecomment-87747209  

I will merge this into master today.  
As for the other two, I don't feel confident enough in my MPL-foo to review those.

---
