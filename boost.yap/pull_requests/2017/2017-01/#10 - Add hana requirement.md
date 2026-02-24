# #10 Add hana requirement [Merged]

> Username: pfultz2  
> Created at: 2017-01-12 20:18:09 UTC  
> Updated at: 2018-06-11 17:16:17 UTC  
> Merged at: 2017-01-16 17:28:48 UTC  
> Closed at: 2017-01-16 17:28:48 UTC  
> Url: https://github.com/boostorg/yap/pull/10  

So now when I call `cget install tzlaine/yap`, it will install hana as well.

---

## Comment 1

> Username: ldionne  
> Created_at: 2017-01-12 21:22:54 UTC  
> Url: https://github.com/boostorg/yap/pull/10#issuecomment-272287630  

There is a number of way of specifying dependencies, notably conan.io. Should yap support all, none or only some of them? I'm not saying this PR is wrong, but simply that it begs a larger question.

---

## Comment 2

> Username: pfultz2  
> Created_at: 2017-01-12 21:58:15 UTC  
> Url: https://github.com/boostorg/yap/pull/10#issuecomment-272296881  

True, there are a number of ways to do that. However, this is just a file that list the dependencies. It is used by `cget`, but can be read by another tool or by the user to manually install dependencies, as well.   
  
Also, supporting other tools like conan.io would require more work, such as a python build script and/or changes to cmake, which does add more maintenance. Adding a text file with a list of dependencies I don't think is too much extra to maintain, but I could be wrong.

---

## Comment 3

> Username: tzlaine  
> Created_at: 2017-01-14 20:35:15 UTC  
> Url: https://github.com/boostorg/yap/pull/10#issuecomment-272651091  

Paul, this looks fine to me, as long as you can add a comment to the effect that this file applies only to uses of cget, and can safely be ignored otherwise.  Can you add such a note?  Also, there is an optional dependency on TypeIndex.  Can you add that too?

---
