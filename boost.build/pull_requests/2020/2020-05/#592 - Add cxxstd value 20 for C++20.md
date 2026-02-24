# #592 Add cxxstd value 20 for C++20 [Merged]

> Username: Lastique  
> Created at: 2020-05-06 08:16:15 UTC  
> Updated at: 2021-10-02 21:01:46 UTC  
> Merged at: 2020-05-06 15:56:39 UTC  
> Closed at: 2020-05-06 15:56:39 UTC  
> Url: https://github.com/boostorg/build/pull/592  

The -std=c++20 is supported by clang 10.

---

## Comment 1

> Username: Lastique  
> Created_at: 2020-05-06 11:59:12 UTC  
> Url: https://github.com/boostorg/build/pull/592#issuecomment-624607261  

If possible, please merge it soon as I've already modified CI settings in a few submodules to use "20", not knowing Boost.Build doesn't support it yet.

---

## Review 2 [Changes requested]

> Username: grafikrobot  
> Created_at: 2020-05-06 12:32:27 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/build/pull/592#pullrequestreview-406564885  

First, and I know you know this.. C++20 is not final yet. But given that gcc is already accepting the option I guess we have to deal with it. Second.. A PR that adds to cxxstd also needs to have the toolset changes to use the new value correctly.  I.e. this needs to also include the gcc (and possibly clang) changes to add the option.

---

## Comment 3

> Username: Lastique  
> Created_at: 2020-05-06 12:54:36 UTC  
> Url: https://github.com/boostorg/build/pull/592#issuecomment-624631529  

C++20 is practically finalized.  
  
Re toolset changes, do you mean to update "latest"? The "20" value itself doesn't seem to need any changes in the toolsets.

---

## Comment 4

> Username: Lastique  
> Created_at: 2020-05-06 12:59:25 UTC  
> Url: https://github.com/boostorg/build/pull/592#issuecomment-624633882  

I've updated `cxxstd=latest` for clang. For gcc it is already set for 2a for gcc 8 and newer.

---

## Comment 5

> Username: grafikrobot  
> Created_at: 2020-05-06 13:26:05 UTC  
> Url: https://github.com/boostorg/build/pull/592#issuecomment-624647949  

> Re toolset changes, do you mean to update "latest"? The "20" value itself doesn't seem to need any changes in the toolsets.  
  
I did mean to update the "latest" marker... But we don't change it to "2a" as that's not a final release. For clang-10 it would mean changing latest == 20. Before that it would stay at 11, 14, or 17 as appropriate. Is there a `2b` option in clang-10 now? If there is, we would also need to add that as a value.

---

## Comment 6

> Username: Lastique  
> Created_at: 2020-05-06 14:39:36 UTC  
> Url: https://github.com/boostorg/build/pull/592#issuecomment-624689126  

> But we don't change it to "2a" as that's not a final release. For clang-10 it would mean changing latest == 20.  
  
I followed the existing practice re "latest" - set it to the latest C++ version supported by a compiler version, including preliminary tags like "2a". `-std=c++2a` is supported since [clang 6](https://releases.llvm.org/6.0.0/tools/clang/docs/ReleaseNotes.html#id9). I can set it to "20" for clang-10, but I don't really know which compiler versions introduced the final tags like 11, 14 and 17.  
  
> Is there a 2b option in clang-10 now?  
  
I'm not aware of such, I don't think there is.

---

## Comment 7

> Username: Lastique  
> Created_at: 2020-05-06 15:01:58 UTC  
> Url: https://github.com/boostorg/build/pull/592#issuecomment-624702503  

I've updated "latest" mapping as you requested.

---

## Comment 8

> Username: Lastique  
> Created_at: 2020-05-06 16:06:51 UTC  
> Url: https://github.com/boostorg/build/pull/592#issuecomment-624740268  

Thanks.

---

## Comment 9

> Username: grafikrobot  
> Created_at: 2020-05-06 16:15:35 UTC  
> Url: https://github.com/boostorg/build/pull/592#issuecomment-624745024  

Thank you for the contribution. And for putting up with me :-)

---

## Comment 10

> Username: Lastique  
> Created_at: 2020-05-08 09:19:41 UTC  
> Url: https://github.com/boostorg/build/pull/592#issuecomment-625725081  

Could you merge this to master, please?

---

## Comment 11

> Username: grafikrobot  
> Created_at: 2020-05-08 12:33:10 UTC  
> Url: https://github.com/boostorg/build/pull/592#issuecomment-625793598  

Short answer is no. Long answer is.. When I can do the next B2 release it will be merged to master. That might be a while (4 weeks)  since I'm rather busy ATM with work deadlines.

---

## Comment 12

> Username: Lastique  
> Created_at: 2020-06-23 22:35:19 UTC  
> Url: https://github.com/boostorg/build/pull/592#issuecomment-648463551  

The 1.74 release is imminent. Please, merge to master.

---

## Comment 13

> Username: grafikrobot  
> Created_at: 2020-06-23 23:22:43 UTC  
> Url: https://github.com/boostorg/build/pull/592#issuecomment-648478189  

Thanks for the reminder. Have some minor changes pending and then merge to master. So it will be within the next few days.

---

## Comment 14

> Username: github-actions[bot]  
> Created_at: 2021-10-02 21:01:45 UTC  
> Url: https://github.com/boostorg/build/pull/592#issuecomment-932819958  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2 Please consider following up at https://github.com/bfgroup/b2/pulls

---
