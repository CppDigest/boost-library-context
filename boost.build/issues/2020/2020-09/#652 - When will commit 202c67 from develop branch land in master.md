# #652 - When will commit 202c67 from develop branch land in master? [Closed]

> Username: Gei0r  
> Created at: 2020-09-04 12:25:09 UTC  
> Updated at: 2020-09-05 10:20:00 UTC  
> Closed at: 2020-09-05 10:19:59 UTC  
> Url: https://github.com/boostorg/build/issues/652  

For building `b2` with clang on Windows, `bootstrap.bat` has to be changed to accept `clang` as a toolchain.  
  
Such a change has already been made in https://github.com/boostorg/boost/commit/202c67acdd6033c431a2b6ad776bdf480be898d8 (Feb 14, 2020), but this change has not yet come to the `master` branch.  
  
Can you bring this change to master?

---

## Comment 1

> Username: grafikrobot  
> Created at: 2020-09-04 14:29:35 UTC  
> Url: https://github.com/boostorg/build/issues/652#issuecomment-687180816  

@mclow I can't put that change in master. I don't have that kind of permission. One of the Boost admins, or release managers, needs to do it.

---

## Comment 2

> Username: pdimov  
> Created at: 2020-09-05 10:19:59 UTC  
> Url: https://github.com/boostorg/build/issues/652#issuecomment-687585365  

I merged https://github.com/boostorg/boost/commit/202c67acdd6033c431a2b6ad776bdf480be898d8 and the accompanying https://github.com/boostorg/boost/pull/371/commits/26a61c10d1d74b20daf12a6a1f3d527f84cec239 to master.
