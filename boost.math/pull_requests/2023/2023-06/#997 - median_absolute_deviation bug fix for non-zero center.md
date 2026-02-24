# #997 median_absolute_deviation bug fix for non-zero center [Merged]

> Username: rasmushenningsson  
> Created at: 2023-06-16 06:08:09 UTC  
> Updated at: 2023-06-19 07:18:17 UTC  
> Merged at: 2023-06-19 07:18:16 UTC  
> Closed at: 2023-06-19 07:18:16 UTC  
> Url: https://github.com/boostorg/math/pull/997  

This fixes a bug in `median_absolute_deviation` causing incorrect answers when a non-zero center is used. (Note that the default center is the median, not zero, so this bug should affect most users of `median_absolute_deviation`.)  
  
This pull request also provides unit tests for the non-zero center case.

---

## Review 1 [Approved]

> Username: mborland  
> Created_at: 2023-06-16 07:35:51 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/math/pull/997#pullrequestreview-1482907872  

LGTM. Thanks for this. I approved the CI run; you can ignore the CircleCI failure.

---

## Comment 2

> Username: NAThompson  
> Created_at: 2023-06-16 15:15:40 UTC  
> Updated_at: 2023-06-16 15:15:53 UTC  
> Url: https://github.com/boostorg/math/pull/997#issuecomment-1594857007  

@rasmushenningsson : Sincerest thanks for fixing this-at this point I shouldn't be surprised by the sheer stupidity of the mistakes I make . . . but for this was I was!

---

## Comment 3

> Username: rasmushenningsson  
> Created_at: 2023-06-19 00:50:05 UTC  
> Url: https://github.com/boostorg/math/pull/997#issuecomment-1596333382  

We all make mistakes. :slightly_smiling_face:   
Thanks for the quick responses and all your work on boost::math!

---
