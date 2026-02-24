# #454 CI: Avoid huge logs due to compilation warnings [Merged]

> Username: mloskot  
> Created at: 2020-03-16 23:33:39 UTC  
> Updated at: 2020-03-17 09:46:38 UTC  
> Merged at: 2020-03-17 09:44:47 UTC  
> Closed at: 2020-03-17 09:44:47 UTC  
> Url: https://github.com/boostorg/gil/pull/454  

<!-- Pull Requests MUST come from topic branch based on develop, and NEVER on `master) --->  
  
### Description  
  
Remove setting flags specific to `CMAKE_CXX_CLANG_TIDY` as likely not necessary  
- restore if it causes issues.  
  
If `CI` (Travis CI, AppVeyor, CircleCI) or `AGENT_JOBSTATUS` (Azure Pipelines) or `GITHUB_ACTIONS` (GitHub Actions) environment variable is defined, then do not set high compiler warning levels.  
  
### Tasklist  
  
<!-- Add YOUR OWN TASK(s), especially if your PR is a work in progress -->  
  
- [x] Ensure all CI builds pass

---
