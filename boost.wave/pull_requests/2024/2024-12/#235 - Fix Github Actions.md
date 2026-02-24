# #235 Fix Github Actions [Merged]

> Username: Flamefire  
> Created at: 2024-12-06 15:39:44 UTC  
> Updated at: 2025-04-28 20:48:36 UTC  
> Merged at: 2024-12-09 22:50:23 UTC  
> Closed at: 2024-12-09 22:50:23 UTC  
> Url: https://github.com/boostorg/wave/pull/235  



---

## Comment 1

> Username: jefftrull  
> Created_at: 2024-12-09 17:15:07 UTC  
> Url: https://github.com/boostorg/wave/pull/235#issuecomment-2528787393  

Can you briefly explain these changes? I know we were wrestling with node versions and support for old compilers last year. This PR reverts one of my workarounds/hacks for that issue. How does this one deal with the problem? Thanks.

---

## Comment 2

> Username: Flamefire  
> Created_at: 2024-12-09 18:37:23 UTC  
> Url: https://github.com/boostorg/wave/pull/235#issuecomment-2529054990  

Sure. This was part of a bulk change over almost 40 repos, hence I've been very brief on description. In short:  
  
macos-12 runners are dropped by github and jobs won't start anymore. at least macos-13 needs to be used  
  
Containers need to be run on the latest ubuntu runner to avoid trouble, e.g. github removing the host runner os or IIRC some stuff wasn't supported on older OS.  
  
Support for node 16 was fully dropped by GitHub --> @v3 actions don't work anymore and `$ACTIONS_ALLOW_USE_UNSECURE_NODE_VERSION` has no effect. So I removed the variable and updated to @v4 actions which require node 20. Github bind-mounts a node20 installation into the container which is incompatible with the glibc of ubuntu 16/18 and due to that mount an installation in the docker image won't work.  
  
This solution downloads an unofficial node 20 built against the correct/older glibc and creates 2 mount points such that we can put that node onto the host runner and mount that instead of what github gives us using the idea from https://github.com/dixyes/ghactionsplay/blob/bf889ee2fed8dcd72cc8e32bfd4789be177c958f/.github/workflows/glibc217node20.yml  
To avoid overloading the unofficial-builds.nodejs.org server we use a copy hosted at boost.io  
  
Does that answer your questions or do you need more details?

---

## Comment 3

> Username: jefftrull  
> Created_at: 2024-12-09 22:51:17 UTC  
> Url: https://github.com/boostorg/wave/pull/235#issuecomment-2529705166  

This fix supersedes the previous hack in #217 thanks @Flamefire

---

## Comment 4

> Username: jefftrull  
> Created_at: 2025-04-25 13:47:59 UTC  
> Url: https://github.com/boostorg/wave/pull/235#issuecomment-2830489264  

Finally getting around to merging into master and I wondered if you had any insights on the CI failure. Clang, so doesn't seem related to this PR but thought I would ask https://github.com/boostorg/wave/actions/runs/14656737135

---

## Comment 5

> Username: Flamefire  
> Created_at: 2025-04-25 13:54:13 UTC  
> Url: https://github.com/boostorg/wave/pull/235#issuecomment-2830505692  

See the message at the bottom of the run:  
> This is a scheduled Ubuntu 20.04 retirement. Ubuntu 20.04 LTS runner will be removed on 2025-04-15.   
  
So the failure is "intended".  
  
I opened https://github.com/boostorg/wave/pull/239 for an update.

---

## Comment 6

> Username: jefftrull  
> Created_at: 2025-04-28 20:48:35 UTC  
> Url: https://github.com/boostorg/wave/pull/235#issuecomment-2836555934  

Thanks again for your many contributions!

---
