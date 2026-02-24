# #332 Docs: download extension [Merged]

> Username: sdarwin  
> Created at: 2025-12-31 23:30:55 UTC  
> Updated at: 2026-01-04 10:18:49 UTC  
> Merged at: 2026-01-04 10:18:49 UTC  
> Closed at: 2026-01-04 10:18:49 UTC  
> Url: https://github.com/boostorg/unordered/pull/332  

When building antora docs, retry the UI download if it fails.  
  
Generally, any network downloads in any build scripts should be configured to retry.     
  
For example, `curl` has options `--retry` and `--retry-all-errors`.     
  
source code: https://github.com/cppalliance/antora-downloads-extension

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2025-12-31 23:37:59 UTC  
> Updated_at: 2026-01-03 16:16:37 UTC  
> Url: https://github.com/boostorg/unordered/pull/332#issuecomment-3703052972  

An automated preview of the documentation is available at [https://332.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/index.html](https://332.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/index.html)  
  
If more commits are pushed to the pull request, the docs will rebuild at the same URL.  
  
2026-01-03 16:16:36 UTC

---

## Comment 2

> Username: joaquintides  
> Created_at: 2026-01-03 15:53:17 UTC  
> Url: https://github.com/boostorg/unordered/pull/332#issuecomment-3707149653  

Could you plase update `package-lock.json` before I merge? See https://github.com/boostorg/unordered/pull/335

---
