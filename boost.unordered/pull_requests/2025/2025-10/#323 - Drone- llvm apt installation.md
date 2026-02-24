# #323 Drone: llvm apt installation [Merged]

> Username: sdarwin  
> Created at: 2025-10-13 15:59:57 UTC  
> Updated at: 2025-10-15 07:40:05 UTC  
> Merged at: 2025-10-15 07:40:04 UTC  
> Closed at: 2025-10-15 07:40:04 UTC  
> Url: https://github.com/boostorg/unordered/pull/323  

- `apt-key` is deprecated  
- `curl` will retry on more conditions than `wget`  
- move debugging output $DRONE_STAGE_MACHINE to as early as possible in the script  
  
If this goes well and tests are passing, then it's worthwhile to copy over to any other repos also.

---

## Review 1 [Commented]

> Username: pdimov  
> Created_at: 2025-10-13 16:07:20 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/unordered/pull/323#pullrequestreview-3332217131  

📁 .drone.jsonnet

```diff
  38 |                 'uname -a',
  38 |-                 'wget -O - https://apt.llvm.org/llvm-snapshot.gpg.key | apt-key add -',
  39 |+                 'curl -sSL --retry 5 https://apt.llvm.org/llvm-snapshot.gpg.key | sudo gpg --dearmor -o /etc/apt/trusted.gpg.d/llvm-snapshot.gpg',
```

> Username: pdimov  
> Created_at: 2025-10-13 16:07:20 UTC  
> Updated_at: 2025-10-13 16:07:21 UTC  
> Url: https://github.com/boostorg/unordered/pull/323#discussion_r2426763149  

We may no longer need this, actually. I've been using the clangs from the system repos everywhere, they are significantly more reliable than the ones from apt.llvm.org.


---

## Comment 2

> Username: sdarwin  
> Created_at: 2025-10-13 16:49:22 UTC  
> Url: https://github.com/boostorg/unordered/pull/323#issuecomment-3398271475  

> significantly more reliable than the ones from apt.llvm.org  
  
What are the details about this?  Do the versions on apt.llvm.org contain bugs?  
  
There are trade-offs.    
  
apt.llvm.org allows you to use LTS versions of Ubuntu, which are more stable, and even keep the CI jobs as-is, the same, for as long as it's convenient.   
  
The temporary Ubuntu releases quickly go end-of-life, not supported, removed from service, and you must update the CI jobs yet again.    
  
It may be, even with these tradeoffs, you still choose the short-term system repos.

---

## Comment 3

> Username: pdimov  
> Created_at: 2025-10-13 16:58:23 UTC  
> Url: https://github.com/boostorg/unordered/pull/323#issuecomment-3398311190  

I use the LTS repos, except for the very last version (just gcc-15 at the moment; clang-20 is already in 24.04, whereas clang-21 is too new even for 25.04.)

---

## Comment 4

> Username: sdarwin  
> Created_at: 2025-10-13 17:00:30 UTC  
> Url: https://github.com/boostorg/unordered/pull/323#issuecomment-3398319868  

> clang-21 is too new even for 25.04.  
  
25.10... Try it out.

---

## Comment 5

> Username: pdimov  
> Created_at: 2025-10-13 17:04:44 UTC  
> Url: https://github.com/boostorg/unordered/pull/323#issuecomment-3398342313  

https://packages.ubuntu.com/search?keywords=clang-21&searchon=names&suite=all&section=all

---

## Comment 6

> Username: sdarwin  
> Created_at: 2025-10-13 17:07:33 UTC  
> Url: https://github.com/boostorg/unordered/pull/323#issuecomment-3398358875  

> https://packages.ubuntu.com/search?keywords=clang-21&searchon=names&suite=all&section=all  
  
Yes, it's available. questing=25.10.

---

## Comment 7

> Username: pdimov  
> Created_at: 2025-10-13 17:07:56 UTC  
> Url: https://github.com/boostorg/unordered/pull/323#issuecomment-3398360585  

Oh wait, questing is 25.10 and has been released a few days ago. Impressive turnaround on the images, Sam. :-)

---

## Comment 8

> Username: sdarwin  
> Created_at: 2025-10-14 20:49:42 UTC  
> Url: https://github.com/boostorg/unordered/pull/323#issuecomment-3403571140  

should this PR be merged? My opinion is  
  
1. at this moment, apt.llvm.org is still in the drone file. This PR improves that in small way.  So - yes.  
2. Adding $DRONE_STAGE_MACHINE debug variables - yes.  
  
then if you prefer, other changes can be done later.

---
