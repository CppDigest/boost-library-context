# #9 Fix unused typedef warning in state_machine.hpp [Merged]

> Username: Romain-Geissler-1A  
> Created at: 2016-05-20 17:05:24 UTC  
> Updated at: 2017-12-21 13:03:48 UTC  
> Merged at: 2017-12-21 13:03:48 UTC  
> Closed at: 2017-12-21 13:03:48 UTC  
> Url: https://github.com/boostorg/msm/pull/9  

Hi,  
  
This small patch fixes a local unused typedef warning with gcc. It is blocking for people using -Wall -Wextra -Werror compilation flags.  
  
All releases from boost 1.58 to boost 1.61 are affected. Note that apparently this code has been refactored in the "develop" branch.  
  
Cheers,  
Romain

---

## Comment 1

> Username: Romain-Geissler  
> Created_at: 2017-11-05 16:45:41 UTC  
> Updated_at: 2017-11-05 16:47:10 UTC  
> Url: https://github.com/boostorg/msm/pull/9#issuecomment-341986922  

Ping @pdimov (It appears you have recently merged things into msm)

---

## Comment 2

> Username: pdimov  
> Created_at: 2017-11-05 17:08:43 UTC  
> Url: https://github.com/boostorg/msm/pull/9#issuecomment-341988529  

No good goes unpunished. :-)

---

## Comment 3

> Username: pdimov  
> Created_at: 2017-11-05 17:28:50 UTC  
> Url: https://github.com/boostorg/msm/pull/9#issuecomment-341990014  

Someone who uses and understands `msm` needs to systematically go through the unmerged changes in `develop`, review them and determine what needs to be merged into `master`.

---

## Comment 4

> Username: pdimov  
> Created_at: 2017-11-05 18:46:05 UTC  
> Url: https://github.com/boostorg/msm/pull/9#issuecomment-341995277  

Ref: https://github.com/boostorg/msm/compare/master...develop

---
