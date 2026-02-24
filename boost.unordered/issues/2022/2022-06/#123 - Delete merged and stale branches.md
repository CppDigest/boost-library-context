# #123 - Delete merged and stale branches [Closed]

> Username: Flamefire  
> Created at: 2022-06-10 09:17:16 UTC  
> Updated at: 2024-08-17 16:54:17 UTC  
> Closed at: 2024-08-17 16:54:17 UTC  
> Url: https://github.com/boostorg/unordered/issues/123  

This repo currently has [55 branches](https://github.com/boostorg/unordered/branches/all)  
  
All branches which are merged to develop (have a "0" in the 2nd number of the table above, i.e. "0 commits ahead of develop") can be safely deleted.  
Also there are branches with no activity in 13-15 years. Some don't have any commits with content (check the "Compare" button), e.g. https://github.com/boostorg/unordered/compare/svn-branches/cpp0x  
  
Others may be so outdated it might make sense to just remove them.  
That makes working with the repo harder and clones/checkouts larger, so a cleanup is due

---

## Comment 1

> Username: k3DW  
> Created at: 2024-08-17 16:54:17 UTC  
> Url: https://github.com/boostorg/unordered/issues/123#issuecomment-2294911935  

I deleted all 32 branches that have "0 ahead". I'll leave all other branches up to the branch owner, and I'll close this issue.
