# #30 Merge 'develop' to 'master' [Merged]

> Username: CromwellEnage  
> Created at: 2019-04-28 21:10:37 UTC  
> Updated at: 2019-08-04 15:25:23 UTC  
> Merged at: 2019-08-04 15:24:42 UTC  
> Closed at: 2019-08-04 15:24:42 UTC  
> Url: https://github.com/boostorg/accumulators/pull/30  

Boost.Parameter changes have now been merged from 'develop' to 'master'.

---

## Comment 1

> Username: CromwellEnage  
> Created_at: 2019-05-18 12:33:52 UTC  
> Url: https://github.com/boostorg/accumulators/pull/30#issuecomment-493673855  

PR #33 fixes the failing checks.

---

## Comment 2

> Username: spacelg  
> Created_at: 2019-07-01 01:53:55 UTC  
> Url: https://github.com/boostorg/accumulators/pull/30#issuecomment-507089353  

Hi All,  
  
Could you please merge this fixes into master branch? It failed for a long time.  
  
Thanks,  
Lin

---

## Comment 3

> Username: swatanabe  
> Created_at: 2019-08-03 23:42:24 UTC  
> Url: https://github.com/boostorg/accumulators/pull/30#issuecomment-517962161  

This is currently causing breakage with the beta.  Can we either apply this or (if all else fails) revert the parameter changes for 1.71?

---

## Comment 4

> Username: lballabio  
> Created_at: 2019-08-04 11:30:11 UTC  
> Url: https://github.com/boostorg/accumulators/pull/30#issuecomment-517995478  

For an example of breakage in the beta, see <https://lists.boost.org/Archives/boost/2019/08/246715.php>.  I tested it with this PR, and it indeed fixes the regression. +1 to merging it before 1.71.

---

## Comment 5

> Username: ericniebler  
> Created_at: 2019-08-04 15:22:53 UTC  
> Updated_at: 2019-08-04 15:25:23 UTC  
> Url: https://github.com/boostorg/accumulators/pull/30#issuecomment-518012402  

What's the story with the failing Travis tests? EDIT: Looks like it's just gcc-4.4. Ok, merging....

---
