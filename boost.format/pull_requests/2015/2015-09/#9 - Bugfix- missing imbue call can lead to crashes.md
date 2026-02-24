# #9 Bugfix: missing imbue call can lead to crashes [Merged]

> Username: joachim-faulhaber  
> Created at: 2015-09-23 14:20:58 UTC  
> Updated at: 2015-09-24 06:51:46 UTC  
> Merged at: 2015-09-23 16:31:07 UTC  
> Closed at: 2015-09-23 16:31:07 UTC  
> Url: https://github.com/boostorg/format/pull/9  



---

## Comment 1

> Username: eldiener  
> Created_at: 2015-09-23 15:19:19 UTC  
> Url: https://github.com/boostorg/format/pull/9#issuecomment-142637174  

Since 'loc_p' can be 0, it needs to be checked for non-zero before being dereferenced.

---

## Comment 2

> Username: joachim-faulhaber  
> Created_at: 2015-09-23 15:47:19 UTC  
> Url: https://github.com/boostorg/format/pull/9#issuecomment-142644565  

Hmm, I thought that's what is done in the patch  
`if(loc_p) oss.imbue(*loc_p);`  
Would you prefer `if(loc_p != nullptr)` ?  
BTW, here is a related ticket containing some more information on the issue:  
https://svn.boost.org/trac/boost/ticket/7435

---

## Comment 3

> Username: eldiener  
> Created_at: 2015-09-23 16:30:30 UTC  
> Url: https://github.com/boostorg/format/pull/9#issuecomment-142655113  

You are right. That's my mistake.

---
