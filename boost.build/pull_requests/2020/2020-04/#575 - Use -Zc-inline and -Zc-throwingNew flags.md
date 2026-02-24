# #575 Use /Zc:inline and /Zc:throwingNew flags [Merged]

> Username: Kojoley  
> Created at: 2020-04-23 00:20:53 UTC  
> Updated at: 2021-10-02 21:02:09 UTC  
> Merged at: 2020-05-05 18:37:51 UTC  
> Closed at: 2020-05-05 18:37:51 UTC  
> Url: https://github.com/boostorg/build/pull/575  

Size differences of `b2 toolset=msvc-14.2 variant=release link=shared,static`  
  
flags              | bin.v2/libs   | diff | lib         | diff | dll        | diff  
-------------------|--------------:|-----:|------------:|-----:|-----------:|-----:  
no                 | 1 268 748 304 |      | 393 665 694 |      | 11 630 592 |    
inline	           |   757 974 914 | -40% | 173 383 766 | -56% | 11 615 744 | -0.1%  
inline+throwingNew |   757 909 662 |  -0% | 173 444 188 |  +0% | 11 590 144 | -0.2%  
  
Timings were 765s/732s/757s  
  
Links to docs:  
https://docs.microsoft.com/en-us/cpp/build/reference/zc-inline-remove-unreferenced-comdat?view=vs-2019  
https://docs.microsoft.com/en-us/cpp/build/reference/zc-throwingnew-assume-operator-new-throws?view=vs-2019

---

## Comment 1

> Username: Kojoley  
> Created_at: 2020-05-04 15:18:36 UTC  
> Url: https://github.com/boostorg/build/pull/575#issuecomment-623526993  

Ping  
  
This is better to be merged sooner to give enough time for any potential problem to show up and be fixed (even while I am not expecting to see one).

---

## Review 2 [Changes requested]

> Username: grafikrobot  
> Created_at: 2020-05-05 00:32:06 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/build/pull/575#pullrequestreview-405432966  

📁 src/tools/msvc.jam

```diff
 460 |+     if ! [ MATCH "^(1[01]|\d)\\." : $(version) ]
 461 |+     {
 462 |+         toolset.flags $(toolset).compile CFLAGS $(conditions) : "/Zc:inline" ;
```

> Username: grafikrobot  
> Created_at: 2020-05-05 00:25:32 UTC  
> Updated_at: 2020-05-05 16:18:01 UTC  
> Url: https://github.com/boostorg/build/pull/575#discussion_r419803731  

I would prefer that `/Zc:inline` be either tied to an existing feature or a new feature added for it. It would be surprising to users to add it unconditionally for users. Especially if they specifically want the opposing functionality.

> Username: Kojoley  
> Created_at: 2020-05-05 00:47:30 UTC  
> Updated_at: 2020-05-05 16:18:01 UTC  
> Url: https://github.com/boostorg/build/pull/575#discussion_r419809637  

We can tie this one to `<visibility>hidden`, however I do not understand why `/Zc:forScope` and `/Zc:wchar_t` are applied unconditionally while these you are asking to go under a feature when it is a misfeature and a compiler bug, from my point of view it should be a compiler default, anyway users could `<cxxflags>/Zc:inline-` if they want to enable this compiler bug back.

> Username: grafikrobot  
> Created_at: 2020-05-05 14:41:18 UTC  
> Updated_at: 2020-05-05 16:18:01 UTC  
> Url: https://github.com/boostorg/build/pull/575#discussion_r420162985  

The `forScope` and `wchar_t` are just old. They where added 16 years ago. Not sure we would do those the same way now either. But not sure we can do different now. And for those not sure it matters as no one uses msvc-7 any longer (or at least I hope they don't).

> Username: Kojoley  
> Created_at: 2020-05-05 15:31:13 UTC  
> Updated_at: 2020-05-05 16:18:01 UTC  
> Url: https://github.com/boostorg/build/pull/575#discussion_r420201147  

And you completely ignored `<cxxflags>/Zc:inline-` part :-)  
I do not know how to proceed, adding 18 features (for every conformance flag) looks like a bad idea.

> Username: grafikrobot  
> Created_at: 2020-05-05 15:54:23 UTC  
> Updated_at: 2020-05-05 16:18:01 UTC  
> Url: https://github.com/boostorg/build/pull/575#discussion_r420218061  

Sorry.. brain is fragmented from trying to work at the same time :-) I do agree that adding a bunch of features for a bunch of flags is not desirable. Haven't thought of an alternative though. Okay, I give up :-)

---

📁 src/tools/msvc.jam

```diff
 467 |+     if ! [ MATCH "^(1[0-3]|\d)\\." : $(version) ]
 468 |+     {
 469 |+         toolset.flags $(toolset).compile CFLAGS $(conditions) : "/Zc:throwingNew" ;
```

> Username: grafikrobot  
> Created_at: 2020-05-05 00:31:50 UTC  
> Updated_at: 2020-05-05 16:18:01 UTC  
> Url: https://github.com/boostorg/build/pull/575#discussion_r419805531  

I can see how this could be the default. But like above I would prefer that it there is a way to control if it's added or not. The only current feature to tie it to would be the language level. But that also doesn't seem correct. As only users know what behavior they want.

> Username: Kojoley  
> Created_at: 2020-05-05 00:48:25 UTC  
> Updated_at: 2020-05-05 16:18:01 UTC  
> Url: https://github.com/boostorg/build/pull/575#discussion_r419809849  

The same thing as above, but for this one I do not see any potential existing feature to integrate into


---

## Comment 3

> Username: grafikrobot  
> Created_at: 2020-05-05 15:57:04 UTC  
> Url: https://github.com/boostorg/build/pull/575#issuecomment-624141149  

After thinking about, and thanks for the links and comments... I'll take the changes with only one request. Could you use the `version.version-less` rule instead of the greps? Just to make this more legible and not need more changes int he future.

---

## Comment 4

> Username: github-actions[bot]  
> Created_at: 2021-10-02 21:02:08 UTC  
> Url: https://github.com/boostorg/build/pull/575#issuecomment-932820034  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2 Please consider following up at https://github.com/bfgroup/b2/pulls

---
