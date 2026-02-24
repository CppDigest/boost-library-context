# #297 Update Cray toolset [Merged]

> Username: jlapolla-cray  
> Created at: 2018-04-09 18:22:40 UTC  
> Updated at: 2021-10-02 22:08:14 UTC  
> Merged at: 2018-06-12 12:30:08 UTC  
> Closed at: 2018-06-12 12:30:08 UTC  
> Url: https://github.com/boostorg/build/pull/297  

I've updated the `cray.jam` toolset. All of the Boost.Build tests pass if you comment out the call to `set-cray-feature-defaults` on line 150.  
  
FYI: I am a Cray employee, working to improve Boost support of CCE (Cray Compiler Environment).

---

## Comment 1

> Username: jlapolla-cray  
> Created_at: 2018-04-23 12:28:20 UTC  
> Url: https://github.com/boostorg/build/pull/297#issuecomment-383557419  

Any plans to merge this? We would like to see these updates in the next Boost release.

---

## Comment 2

> Username: grafikrobot  
> Created_at: 2018-04-23 14:13:09 UTC  
> Url: https://github.com/boostorg/build/pull/297#issuecomment-383589883  

Yes, I'm planning on merging this.. Just been busy (and sick) this past few weeks.

---

## Comment 3

> Username: jlapolla-cray  
> Created_at: 2018-05-24 12:20:38 UTC  
> Url: https://github.com/boostorg/build/pull/297#issuecomment-391693704  

This is my polite monthly reminder for May 2018: we are still interested in seeing these changes in an upcoming Boost release.  
  
Let me know about a week before you intend to merge: I would like to rebase and retest to make sure everything works with the latest changes from upstream.

---
