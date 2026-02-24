# #513 - Investigate potential rule related performance problems [Closed]

> Username: djowel  
> Created at: 2019-06-08 23:14:56 UTC  
> Updated at: 2025-05-10 01:39:23 UTC  
> Closed at: 2025-05-10 01:38:53 UTC  
> Url: https://github.com/boostorg/spirit/issues/513  

This commit: https://github.com/boostorg/spirit/commit/afe763b61ece1a9fea5aee419920e8526e6dfcef included a static that forces us to always supply an attribute.   
  
It was noted in https://github.com/boostorg/spirit/issues/511 that the commit was because the code was previously silently synthesizing imposing full attribute manipulation overhead.

---

## Comment 1

> Username: saki7  
> Created at: 2025-05-10 01:38:53 UTC  
> Url: https://github.com/boostorg/spirit/issues/513#issuecomment-2868208540  

I think this issue has effectively been resolved in these years. I've had benchmarked X3 on a various scenarios, but I've not seen any issues regarding attribute overhead.  
  
Closing as stale. Please feel free to reopen if you the issue is still valid.
