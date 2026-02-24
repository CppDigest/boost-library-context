# #2301 Update GitHub Actions CI file [Closed]

> Username: sdarwin  
> Created at: 2021-08-24 14:16:15 UTC  
> Updated at: 2021-09-04 04:38:59 UTC  
> Closed at: 2021-09-04 04:38:59 UTC  
> Url: https://github.com/boostorg/beast/pull/2301  

The Ubuntu 16.04 environment is scheduled to be removed from GitHub Actions in September 2021. Migrating those jobs to Docker containers or Ubuntu 18.04.  
  
One small error appears on gcc-4.8.   In order for the tests to display "passing", I have set "supported: false" on gcc-4.8.    If you could review that error, and possibly fix it, then the job could be re-enabled.

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2021-08-24 14:52:24 UTC  
> Url: https://github.com/boostorg/beast/pull/2301#issuecomment-904712876  

![pullrequest](https://2301.beast.tracing.cppalliance.org/pullrequest-cbe897b4.png)  
Timeline tracing charts: [https://2301.beast.tracing.cppalliance.org/index.html](https://2301.beast.tracing.cppalliance.org/index.html)

---

## Comment 2

> Username: madmongo1  
> Created_at: 2021-09-03 11:31:40 UTC  
> Url: https://github.com/boostorg/beast/pull/2301#issuecomment-912469167  

It's failing to build on drone. What should be done about that? @sdarwin

---

## Comment 3

> Username: sdarwin  
> Created_at: 2021-09-03 11:59:29 UTC  
> Updated_at: 2021-09-03 12:04:35 UTC  
> Url: https://github.com/boostorg/beast/pull/2301#issuecomment-912483197  

> It's failing to build on drone.   
  
Not a problem, that's only a coincidence because the main branches have newer/different drone files than this PR.   It should automatically resolve, after a merge.

---
