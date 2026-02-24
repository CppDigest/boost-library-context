# #21 Small fix for those using -Wsign-conversion [Merged]

> Username: igaztanaga  
> Created at: 2021-10-11 11:18:28 UTC  
> Updated at: 2021-12-30 16:28:45 UTC  
> Merged at: 2021-12-30 15:20:18 UTC  
> Closed at: 2021-12-30 15:20:18 UTC  
> Url: https://github.com/boostorg/tuple/pull/21  



---

## Comment 1

> Username: cmazakas  
> Created_at: 2021-11-15 23:08:31 UTC  
> Url: https://github.com/boostorg/tuple/pull/21#issuecomment-969415266  

What's the likelihood of this being merged? Unordered right now has a lot of output from `-Wconversion` + `tuple.hpp` and this PR fixes it.

---

## Comment 2

> Username: djowel  
> Created_at: 2021-11-16 00:08:03 UTC  
> Url: https://github.com/boostorg/tuple/pull/21#issuecomment-969473372  

> What's the likelihood of this being merged? Unordered right now has a lot of output from `-Wconversion` + `tuple.hpp` and this PR fixes it.  
  
It looks good to me, except of the CI issues. Is this something you can look at?

---

## Comment 3

> Username: cmazakas  
> Created_at: 2021-11-16 17:13:20 UTC  
> Url: https://github.com/boostorg/tuple/pull/21#issuecomment-970485517  

Ah, I think I know the issues with the CI here.  
  
Unfortunately, GitHub Actions doesn't seem to give helpful error messages but they've deprecated Ubuntu 16.04 which this repo is configured to use.  
  
Sam's PRs for this repo should fix the issue but I'm wondering if we don't have some robust GHA/Drone scripts sitting around in some of the other repos.

---

## Comment 4

> Username: pdimov  
> Created_at: 2021-11-16 17:33:44 UTC  
> Url: https://github.com/boostorg/tuple/pull/21#issuecomment-970503647  

I merged Sam's PR fixing GHA, but this PR needs to be rebased against develop to acquire those fixes.

---

## Comment 5

> Username: pdimov  
> Created_at: 2021-11-16 17:41:19 UTC  
> Url: https://github.com/boostorg/tuple/pull/21#issuecomment-970509996  

Here's a GHA run of this PR merged into the current develop: https://github.com/boostorg/tuple/actions/runs/1468081170

---

## Comment 6

> Username: igaztanaga  
> Created_at: 2021-12-30 14:23:16 UTC  
> Url: https://github.com/boostorg/tuple/pull/21#issuecomment-1003051249  

Sorry for the delay, I just forgot to rebase the patch against develop. Seems like all checks are ok now, could we merge?

---

## Comment 7

> Username: igaztanaga  
> Created_at: 2021-12-30 16:28:42 UTC  
> Url: https://github.com/boostorg/tuple/pull/21#issuecomment-1003098392  

Many thanks!

---
