# #831 Revert modernization [Merged]

> Username: saki7  
> Created at: 2025-09-13 00:19:56 UTC  
> Updated at: 2025-09-13 01:24:18 UTC  
> Merged at: 2025-09-13 00:36:40 UTC  
> Closed at: 2025-09-13 00:36:40 UTC  
> Url: https://github.com/boostorg/spirit/pull/831  

As per recent discussion on the Boost developers' mailing list, we have officially decided to continue development on [Spirit.X4](https://github.com/boostorg/spirit_x4).  
  
This PR reverts all the C++23 modernization PRs, and adds the warning and the background information on README.  
  
ref:  
  
- <https://lists.boost.org/archives/list/boost@lists.boost.org/thread/K3EQLEQJHEUBROB6ODUKTZHFP2FDUS2E/?sort=date>  
- #795  
- #807

---

## Comment 1

> Username: saki7  
> Created_at: 2025-09-13 00:36:21 UTC  
> Url: https://github.com/boostorg/spirit/pull/831#issuecomment-3287259357  

I'm merging this right away. The CI on AppVeyor was poorly configured even before the modernization and it takes half a day to finish. This revert contains only the "git revert" and the README changes, so it is trivial that I successfully reverted everything, given the fact that GHA jobs are passing.

---

## Comment 2

> Username: saki7  
> Created_at: 2025-09-13 01:24:18 UTC  
> Url: https://github.com/boostorg/spirit/pull/831#issuecomment-3287337740  

Note: the CI is failing on `develop`, but it was already failing since May. I forgot to mention this fact.  
  
I technically reverted everything. The failure on AppVeyor should be considered spurious.

---
