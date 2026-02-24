# #370 Mark all non-simple build targets of extension/io as explicit [Merged]

> Username: mloskot  
> Created at: 2019-08-07 12:50:07 UTC  
> Updated at: 2019-08-09 08:51:12 UTC  
> Merged at: 2019-08-09 08:50:59 UTC  
> Closed at: 2019-08-09 08:50:59 UTC  
> Url: https://github.com/boostorg/gil/pull/370  

### Description  
  
This addresses issues during the Boost regression runs, where builders only provide popular third-party libraries (i.e. libraw is missing).  
  
### References  
  
<!-- Any links related to this PR: issues, other PRs, mailing list threads, StackOverflow questions, etc. -->  
- @mjcaisse pointed out the problem https://cpplang.slack.com/archives/C27KZLB0X/p1565130435280100  
  
### Tasklist  
  
<!-- Add YOUR OWN TASK(s), especially if your PR is a work in progress -->  
  
- [x] Ensure all CI builds pass  
- [ ] Review and approve

---

## Comment 1

> Username: mloskot  
> Created_at: 2019-08-07 13:00:55 UTC  
> Updated_at: 2019-08-07 13:36:57 UTC  
> Url: https://github.com/boostorg/gil/pull/370#issuecomment-519085856  

@stefanseefeld I believe that @mjcaisse would like to see this merged into the `master` for release with Boost 1.71, so it seems quite urgent to merge ([3 days left](https://www.boost.org/development/index.html) if at all :)).  
  
Once this PR is merged into our `develop`, how are we going to approach merging this into our `master`, cherry-picking? Will you be able to do it?

---

## Comment 2

> Username: mloskot  
> Created_at: 2019-08-09 08:50:52 UTC  
> Url: https://github.com/boostorg/gil/pull/370#issuecomment-519838382  

We have missed the 1.71 release closing date, so I'm merging this to `develop` only and planning for Boost 1.72 or later.  
  
/cc @mjcaisse, @stefanseefeld

---
