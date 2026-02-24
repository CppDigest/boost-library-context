# #58 thread::try_join_until: Avoid busy wait if system clock changes [Closed]

> Username: mikecrowe  
> Created at: 2015-06-22 11:08:37 UTC  
> Updated at: 2015-09-28 19:47:51 UTC  
> Closed at: 2015-09-28 19:47:51 UTC  
> Url: https://github.com/boostorg/thread/pull/58  

If system clock changes by an amount larger than the outstanding duration  
according to the supplied clock then the code loops around again but  
continues to pass a time point based on the original value of the system  
clock to the system_clock variant of try_join.  
  
If we're going to recalculate the outstanding duration in the loop then it  
is necessary to get the current time according to the system clock to use  
with this duration.  
  
Signed-off-by: Mike Crowe mac@mcrowe.com

---

## Comment 1

> Username: viboes  
> Created_at: 2015-09-27 22:12:28 UTC  
> Url: https://github.com/boostorg/thread/pull/58#issuecomment-143598186  

Thanks for catching this issue.  
I could merge it if you create a PR on develop. Otherwise I can just apply the patch manually as it is really simple.

---

## Comment 2

> Username: mikecrowe  
> Created_at: 2015-09-28 09:11:17 UTC  
> Url: https://github.com/boostorg/thread/pull/58#issuecomment-143686481  

https://svn.boost.org/trac/boost/ticket/11688  
  
HTH.

---

## Comment 3

> Username: viboes  
> Created_at: 2015-09-28 19:47:51 UTC  
> Url: https://github.com/boostorg/thread/pull/58#issuecomment-143855534  

https://github.com/boostorg/thread/commit/971c028c9a72ab4de7776bcff9b76c7a7b4e5e81

---
