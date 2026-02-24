# #268 Extensions: add ttmath support for fmod [Merged]

> Username: mkaravel  
> Created at: 2015-03-13 10:24:31 UTC  
> Updated at: 2015-04-28 13:43:37 UTC  
> Merged at: 2015-03-22 16:51:03 UTC  
> Closed at: 2015-03-22 16:51:03 UTC  
> Url: https://github.com/boostorg/geometry/pull/268  

Add support for the `fmod()` free function.

---

## Comment 1

> Username: mkaravel  
> Created_at: 2015-03-13 10:27:00 UTC  
> Url: https://github.com/boostorg/geometry/pull/268#issuecomment-78905329  

As this is a modification on extensions, I think it makes sense to merge it after we are done with boost 1.58.

---

## Comment 2

> Username: awulkiew  
> Created_at: 2015-03-16 12:21:34 UTC  
> Updated_at: 2015-03-22 15:12:27 UTC  
> Url: https://github.com/boostorg/geometry/pull/268#discussion_r26479504  

After the changes done in the other PR here the name should be changed to `mod()` as well.

---

## Comment 3

> Username: mkaravel  
> Created_at: 2015-03-16 13:11:01 UTC  
> Updated_at: 2015-03-22 15:12:27 UTC  
> Url: https://github.com/boostorg/geometry/pull/268#discussion_r26482217  

I am aware of that.  
I was waiting to have the okay for the other PRs before modifying this.  
I was also considering closing this one and creating a new one so as to keep the history simple.  
Will re-act on it once the other PRs are okay/done.

---

## Comment 4

> Username: barendgehrels  
> Created_at: 2015-03-22 10:20:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/268#issuecomment-84580801  

OK for me (with the name mod)

---

## Comment 5

> Username: mkaravel  
> Created_at: 2015-03-22 15:13:23 UTC  
> Url: https://github.com/boostorg/geometry/pull/268#issuecomment-84628151  

Function renamed from `fmod()` to `mod()`. See commit aac92cb.

---
