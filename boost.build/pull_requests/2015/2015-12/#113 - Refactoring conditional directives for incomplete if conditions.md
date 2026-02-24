# #113 Refactoring conditional directives for incomplete if conditions. [Closed]

> Username: wmarquesr  
> Created at: 2015-12-14 05:32:47 UTC  
> Updated at: 2021-10-02 22:20:11 UTC  
> Closed at: 2015-12-14 07:24:59 UTC  
> Url: https://github.com/boostorg/build/pull/113  

A suggestion to compile entire statements and expressions, as suggested by code style guidelines from the Linux Kernel and practitioners.  
- [Linux kernel coding style](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/tree/Documentation/CodingStyle#n892)  
- [Living in the #ifdef Hell](https://www.cqse.eu/en/blog/living-in-the-ifdef-hell/)  
  
It might improve code understanding, maintainability and error-proneness.

---

## Comment 1

> Username: vprus  
> Created_at: 2015-12-14 07:06:15 UTC  
> Url: https://github.com/boostorg/build/pull/113#issuecomment-164364420  

Thanks for your patch, it has been cherry-picked.

---

## Comment 2

> Username: wmarquesr  
> Created_at: 2015-12-14 07:15:42 UTC  
> Url: https://github.com/boostorg/build/pull/113#issuecomment-164365473  

Right, but why refused ?

---

## Comment 3

> Username: vprus  
> Created_at: 2015-12-14 07:24:54 UTC  
> Updated_at: 2015-12-14 07:26:22 UTC  
> Url: https://github.com/boostorg/build/pull/113#issuecomment-164366919  

It's not "refused", it's just GitHub shows pull requests that were cherry-picked as "closed". I prefer to use cherry-pick, not merge, for one-commit pull requests, to keep the history cleaner.

---

## Comment 4

> Username: wmarquesr  
> Created_at: 2015-12-14 07:31:53 UTC  
> Url: https://github.com/boostorg/build/pull/113#issuecomment-164368614  

Oh, right. So you agree with that kind of refactoring ?

---

## Comment 5

> Username: vprus  
> Created_at: 2015-12-14 07:40:12 UTC  
> Url: https://github.com/boostorg/build/pull/113#issuecomment-164369677  

Yes, this refactoring makes code cleaner.  
  
Just to clear up any possible confusion - your code is on develop branch now, see: https://github.com/boostorg/build/commits/develop  
  
Git has two ways to process pull requests - "merge", which GitHub shows as "merged", and "cherry-pick", which GitHub shows as "closed". However, in both cases your code is on the development branch, in exactly the same state.  
  
Thanks again for your contribution!

---
