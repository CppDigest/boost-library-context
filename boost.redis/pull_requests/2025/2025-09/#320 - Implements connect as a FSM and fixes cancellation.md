# #320 Implements connect as a FSM and fixes cancellation [Merged]

> Username: anarthal  
> Created at: 2025-09-27 18:31:47 UTC  
> Updated at: 2025-10-06 10:39:32 UTC  
> Merged at: 2025-10-06 10:39:29 UTC  
> Closed at: 2025-10-06 10:39:29 UTC  
> Url: https://github.com/boostorg/redis/pull/320  

Implements redis_stream::async_connect as a FSM  
Adds per-operation cancellation handling code  
Adds tests

---

## Comment 1

> Username: anarthal  
> Created_at: 2025-09-27 18:33:18 UTC  
> Url: https://github.com/boostorg/redis/pull/320#issuecomment-3341967938  

This is an early prototype yet. I've started implementing #319 and it was getting too big and messy around `redis_stream::async_connect`. This implements the required code to handle per-operation cancellation correctly in `async_connect`, while covering this code with tests.

---

## Comment 2

> Username: anarthal  
> Created_at: 2025-10-03 18:11:10 UTC  
> Url: https://github.com/boostorg/redis/pull/320#issuecomment-3366736259  

This should fix the last race conditions on cancellation and add some coverage.

---

## Comment 3

> Username: mzimbres  
> Created_at: 2025-10-03 19:52:44 UTC  
> Url: https://github.com/boostorg/redis/pull/320#issuecomment-3367025649  

Hi Ruben, thanks. This is something I forgot to ask you before: can you please rebase on top of `develop` before opening a PR and merging? This results in simpler history and it is easier for me to review the PR locally. I think it will be too complex for this PR so we can do this for the next PRs.

---

## Comment 4

> Username: anarthal  
> Created_at: 2025-10-03 20:04:26 UTC  
> Url: https://github.com/boostorg/redis/pull/320#issuecomment-3367053704  

I think this one is actually good - it's just ultra segmented in terms of commits, but they're all part of the PR. I always do squash merging when merging, so these won't appear in history - just a big commit replacing them.  
  
I did something wrong in the cancel_after PR which I think I've solved now.

---

## Comment 5

> Username: anarthal  
> Created_at: 2025-10-03 20:04:58 UTC  
> Url: https://github.com/boostorg/redis/pull/320#issuecomment-3367055095  

(looks like I commited once per test and wrote about 20 tests, so... :))

---
