# #474 Remove dependencies on Boost.StaticAssert [Merged]

> Username: Lastique  
> Created at: 2026-01-22 16:59:04 UTC  
> Updated at: 2026-02-09 19:59:51 UTC  
> Merged at: 2026-02-09 19:33:26 UTC  
> Closed at: 2026-02-09 19:33:26 UTC  
> Url: https://github.com/boostorg/test/pull/474  

Boost.StaticAssert has been merged into Boost.Config, so remove the dependency.

---

## Comment 1

> Username: mborland  
> Created_at: 2026-01-22 19:37:40 UTC  
> Url: https://github.com/boostorg/test/pull/474#issuecomment-3786330424  

Looks like this one will need to wait on a bunch of other libs to merge first: https://github.com/boostorg/test/actions/runs/21257289737/job/61175300314?pr=474

---

## Comment 2

> Username: Lastique  
> Created_at: 2026-01-22 20:48:47 UTC  
> Updated_at: 2026-01-22 20:50:10 UTC  
> Url: https://github.com/boostorg/test/pull/474#issuecomment-3786624747  

Yes, probably. The problem seems to be because `test/cmake_test/CMakeLists.txt` manually adds subdirectories of the dependent libraries without tracking dependencies, and some of the dependent libraries have not yet merged the PRs removing the Boost.StaticAssert dependencies. This should resolve once those PRs are merged.

---

## Comment 3

> Username: Lastique  
> Created_at: 2026-02-09 19:30:11 UTC  
> Url: https://github.com/boostorg/test/pull/474#issuecomment-3873458817  

The dependencies have merged their PRs and the CI is passing now.

---
