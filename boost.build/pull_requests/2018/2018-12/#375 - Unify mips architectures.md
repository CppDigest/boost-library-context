# #375  Unify mips architectures [Closed]

> Username: FlyGoat  
> Created at: 2018-12-02 05:10:31 UTC  
> Updated at: 2021-10-02 20:59:17 UTC  
> Closed at: 2021-07-23 02:19:43 UTC  
> Url: https://github.com/boostorg/build/pull/375  



---

## Comment 1

> Username: FlyGoat  
> Created_at: 2018-12-02 05:14:11 UTC  
> Url: https://github.com/boostorg/build/pull/375#issuecomment-443482402  

These four PRs should be accepted together:  
- https://github.com/boostorg/build/pull/375  
- https://github.com/boostorg/config/pull/254  
- https://github.com/boostorg/context/pull/93  
- https://github.com/boostorg/log/pull/66

---

## Comment 2

> Username: grafikrobot  
> Created_at: 2018-12-02 05:20:36 UTC  
> Url: https://github.com/boostorg/build/pull/375#issuecomment-443482641  

It's not possible to accept PRs together. Is it critical that they all be accepted at once? If so, why?

---

## Comment 3

> Username: grafikrobot  
> Created_at: 2018-12-02 05:26:31 UTC  
> Url: https://github.com/boostorg/build/pull/375#issuecomment-443482874  

Looking at the other non-b2 PRs I can see this can be coordinated better by:  
  
1. Adding the general "mips" architecture without removing the rest. We would need to indicate they non-general as deprecated somehow.  
2. Apply the non-b2 PRs.  
3. Do another PR to remove the non-general mips architectures at some future point.

---

## Comment 4

> Username: FlyGoat  
> Created_at: 2018-12-02 05:56:09 UTC  
> Url: https://github.com/boostorg/build/pull/375#issuecomment-443483948  

> 1. Adding the general "mips" architecture without removing the rest. We would need to indicate they non-general as deprecated somehow.  
  
Thanks for your suggestions, where should be the best place to add such a warning?  
  
It would be better if these PRs can be accepted at once so that build the whole boost on mips won't be broken.

---

## Comment 5

> Username: stale[bot]  
> Created_at: 2021-05-29 16:21:49 UTC  
> Url: https://github.com/boostorg/build/pull/375#issuecomment-850859494  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.

---

## Comment 6

> Username: Lastique  
> Created_at: 2021-06-10 13:35:24 UTC  
> Url: https://github.com/boostorg/build/pull/375#issuecomment-858628776  

@FlyGoat Is this no longer relevant? Should the other PRs be closed as well?

---

## Comment 7

> Username: FlyGoat  
> Created_at: 2021-07-23 02:16:01 UTC  
> Url: https://github.com/boostorg/build/pull/375#issuecomment-885354289  

@Lastique Sorry my fault, I'll rebase and reopen it. Yes, it's still a problem.

---

## Comment 8

> Username: FlyGoat  
> Created_at: 2021-07-23 02:19:43 UTC  
> Url: https://github.com/boostorg/build/pull/375#issuecomment-885355336  

@wzssyqa handed will sucess this PR.

---

## Comment 9

> Username: github-actions[bot]  
> Created_at: 2021-10-02 20:59:16 UTC  
> Url: https://github.com/boostorg/build/pull/375#issuecomment-932819614  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2 Please consider following up at https://github.com/bfgroup/b2/pulls

---
