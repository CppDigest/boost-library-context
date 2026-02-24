# #262 Fix: remove obsolete includes referring to the "multi"directory [Merged]

> Username: mkaravel  
> Created at: 2015-03-11 17:45:50 UTC  
> Updated at: 2015-03-12 00:27:18 UTC  
> Merged at: 2015-03-11 18:05:05 UTC  
> Closed at: 2015-03-11 18:05:05 UTC  
> Url: https://github.com/boostorg/geometry/pull/262  



---

## Comment 1

> Username: barendgehrels  
> Created_at: 2015-03-11 18:06:03 UTC  
> Url: https://github.com/boostorg/geometry/pull/262#issuecomment-78327690  

Can't we remove the files themselves too?

---

## Comment 2

> Username: awulkiew  
> Created_at: 2015-03-11 18:11:59 UTC  
> Url: https://github.com/boostorg/geometry/pull/262#issuecomment-78330424  

They were left for backward compatibility.  
Do you think we should remove them?  
All of them or e.g. only details?

---

## Comment 3

> Username: barendgehrels  
> Created_at: 2015-03-11 18:17:44 UTC  
> Url: https://github.com/boostorg/geometry/pull/262#issuecomment-78336055  

I know but nobody (or hardly anybody) includes them directly, and they are already empty for several releases IIRC.  
But this is indeed a "major change" possibly having backward compatability issues, so let's do it after the 1.58 release and put it in the notes there.

---

## Comment 4

> Username: mkaravel  
> Created_at: 2015-03-11 18:36:53 UTC  
> Url: https://github.com/boostorg/geometry/pull/262#issuecomment-78341832  

I am doing the `#include` removals as I see them when working on code.  
I think we should do a pass and remove all of them in our code.  
This is independent of removing the files, which I agree should be done after 1.58 and not under time pressure.  
I volunteer to remove the obsolete includes all over the library.

---

## Comment 5

> Username: barendgehrels  
> Created_at: 2015-03-11 18:47:13 UTC  
> Url: https://github.com/boostorg/geometry/pull/262#issuecomment-78343857  

@mkaravel that is OK, thanks for volunteering

---
