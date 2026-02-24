# #120 s390x architecture drone tests [Closed]

> Username: sdarwin  
> Created at: 2022-02-03 22:23:40 UTC  
> Updated at: 2022-02-21 19:52:04 UTC  
> Closed at: 2022-02-21 19:52:04 UTC  
> Url: https://github.com/boostorg/url/pull/120  

[IBM LinuxONE Community Cloud](https://developer.ibm.com/gettingstarted/ibm-linuxone/) offers 120-day access to a s390x architecture virtual server.       
These drone jobs will run in that temp. environment.  I will contact them and investigate more long-term options.      
You might merge this pull request into a different branch, a feature/s390x branch, and keep it separate for now.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2022-02-04 01:22:38 UTC  
> Url: https://github.com/boostorg/url/pull/120#issuecomment-1029553884  

# [Codecov](https://codecov.io/gh/CPPAlliance/url/pull/120?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) Report  
> Merging [#120](https://codecov.io/gh/CPPAlliance/url/pull/120?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) (bbd568c) into [develop](https://codecov.io/gh/CPPAlliance/url/commit/8911d05c35123f978e90321fea863385b63525d4?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) (8911d05) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/CPPAlliance/url/pull/120/graphs/tree.svg?width=650&height=150&src=pr&token=jUkdNr0eJi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)](https://codecov.io/gh/CPPAlliance/url/pull/120?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #120   +/-   ##  
========================================  
  Coverage    96.22%   96.22%             
========================================  
  Files          104      104             
  Lines         5486     5486             
========================================  
  Hits          5279     5279             
  Misses         207      207             
```  
  
  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/CPPAlliance/url/pull/120?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/CPPAlliance/url/pull/120?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance). Last update [8911d05...bbd568c](https://codecov.io/gh/CPPAlliance/url/pull/120?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance).

---

## Comment 2

> Username: vinniefalco  
> Created_at: 2022-02-21 17:24:39 UTC  
> Url: https://github.com/boostorg/url/pull/120#issuecomment-1047097139  

So what happened with the investigation?

---

## Comment 3

> Username: sdarwin  
> Created_at: 2022-02-21 17:57:38 UTC  
> Url: https://github.com/boostorg/url/pull/120#issuecomment-1047118679  

> So what happened with the investigation?  
  
Instead of Community Cloud, s390x jobs are now running at cloud.ibm.com on their new VPC Infrastructure, Tokyo region. Drone doesn't include an IBM autoscaler currently, instances are provisioned vm's.  
Tests passed. You could merge the pull request.

---

## Comment 4

> Username: vinniefalco  
> Created_at: 2022-02-21 19:40:15 UTC  
> Url: https://github.com/boostorg/url/pull/120#issuecomment-1047179441  

The codecov target should always come first in the matrix, so we can see that report quickly. I will fix the drone.star file.

---
