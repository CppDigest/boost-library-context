# #72 Remove duplicate conditional check. [Merged]

> Username: ya1gaurav  
> Created at: 2015-11-03 08:58:43 UTC  
> Updated at: 2015-11-06 18:35:44 UTC  
> Merged at: 2015-11-06 18:35:44 UTC  
> Closed at: 2015-11-06 18:35:44 UTC  
> Url: https://github.com/boostorg/thread/pull/72  

thread_info already checked against Null at line no 86, no need to check again.

---

## Comment 1

> Username: viboes  
> Created_at: 2015-11-06 18:35:38 UTC  
> Url: https://github.com/boostorg/thread/pull/72#issuecomment-154495066  

As you see There was a comment `// fixme: should we test this?`.  
  
Some time ago when I removed this check I got in trouble. After checking this seems to work now :)

---
