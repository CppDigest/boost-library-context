# #803 Fix duplicates using custom compare with bitonic block sort [Merged]

> Username: rosenrodt  
> Created at: 2018-12-19 09:12:25 UTC  
> Updated at: 2018-12-20 08:32:31 UTC  
> Merged at: 2018-12-20 08:32:31 UTC  
> Closed at: 2018-12-20 08:32:31 UTC  
> Url: https://github.com/boostorg/compute/pull/803  

I have modified test case to reproduce the issue as described in #774. And a fix is proposed  
  
Tested on:  
* Nvidia Geforce GTX1060   
* AMD Radeon HD 6770

---

## Comment 1

> Username: jszuppe  
> Created_at: 2018-12-19 10:59:12 UTC  
> Url: https://github.com/boostorg/compute/pull/803#issuecomment-448554540  

Thanks! I'll look into today or later this week and merge.

---

## Comment 2

> Username: jszuppe  
> Created_at: 2018-12-19 11:01:30 UTC  
> Url: https://github.com/boostorg/compute/pull/803#issuecomment-448555173  

It would be great if you can change the order of commits: first test which shows the bug, then commit with a fix. Please reference issue id in the fix commit. If you have problems with doing that, or don't know how to do it using git, don't worry, I'll do that for you. Thanks again for submitting a fix and testing it on two platforms!

---

## Comment 3

> Username: rosenrodt  
> Created_at: 2018-12-19 11:24:08 UTC  
> Url: https://github.com/boostorg/compute/pull/803#issuecomment-448561262  

I’ll do that once I get back to my computer. Do I simply force-push to rewrite the git history?

---

## Comment 4

> Username: jszuppe  
> Created_at: 2018-12-19 11:25:42 UTC  
> Url: https://github.com/boostorg/compute/pull/803#issuecomment-448561657  

Yes, force push will work.

---

## Comment 5

> Username: rosenrodt  
> Created_at: 2018-12-19 13:31:49 UTC  
> Url: https://github.com/boostorg/compute/pull/803#issuecomment-448597656  

@jszuppe thanks. i've rearranged the commits

---

## Comment 6

> Username: jszuppe  
> Created_at: 2018-12-19 19:57:48 UTC  
> Updated_at: 2018-12-19 19:58:18 UTC  
> Url: https://github.com/boostorg/compute/pull/803#issuecomment-448724374  

After looking at it.  
  
1. Will it work correctly if we do opposite: force them not to do the swap if they are equal? This is always one local memory operation less. I think the bug is/was that if there are equal values one is swapped and one is not.  
2. If 1. does not work, then I think we can at least limit your fix to `sort_by_key` situation only.  
  
But in generally fix is ok.

---

## Comment 7

> Username: rosenrodt  
> Created_at: 2018-12-20 02:00:36 UTC  
> Url: https://github.com/boostorg/compute/pull/803#issuecomment-448830916  

1. That will do too. I have pushed them too in the PR   
2. The fix concerns `bitonic_block_sort` with `sort_by_key = false`. So I am not sure what you mean by that. Although I haven't looked into it but does the CPU equivalent or `bitonic_block_sort` with `sort_by_key = true` could possibly repro issue #774 too?  
  
btw the Travis build server seems to complain about missing `CL.h`

---

## Comment 8

> Username: jszuppe  
> Created_at: 2018-12-20 08:31:34 UTC  
> Url: https://github.com/boostorg/compute/pull/803#issuecomment-448915250  

1. Ok  
2. Yeah, you're right. I think it can apply sort-by-key situation too with keys, but of course this fixes also that problem.

---

## Comment 9

> Username: jszuppe  
> Created_at: 2018-12-20 08:32:20 UTC  
> Url: https://github.com/boostorg/compute/pull/803#issuecomment-448915464  

> btw the Travis build server seems to complain about missing CL.h  
  
Yeah, I'll have to fix Travis CI script someday.... Too much work at work ;)

---
