# #644 ci: Switch GitHub Action's MSVC build to supported OS version [Merged]

> Username: striezel  
> Created at: 2022-04-08 23:29:19 UTC  
> Updated at: 2022-04-09 11:59:11 UTC  
> Merged at: 2022-04-09 11:26:41 UTC  
> Closed at: 2022-04-09 11:26:41 UTC  
> Url: https://github.com/boostorg/gil/pull/644  

### Description  
  
The Windows-2016 environment for GitHub Actions has been retired in March 2022, so it's not available anymore. For further information on that see <https://github.com/actions/virtual-environments/issues/4312>. However, we can test on the newer Windows 2022 environment instead. This also brings Visual Studio 2022.  
  
So this pull request removes the GitHub Actions job using the Windows 2016 environment and introduces a new one with the Windows 2022 environment.  
  
### References  
  
See https://github.com/actions/virtual-environments/issues/4312 for the removal announcement of the `windows-2016` environment.  
  
### Tasklist  
  
- [ ] Check that the new CI build with `windows-2022` passes  
- [ ] Review and approve

---

## Comment 1

> Username: striezel  
> Created_at: 2022-04-09 01:30:22 UTC  
> Url: https://github.com/boostorg/gil/pull/644#issuecomment-1093562753  

Hmm...  
  
![Geralt_Hmm](https://user-images.githubusercontent.com/20865852/162551042-c3240cfb-9bbe-4d92-8fc0-6dd7bd0a87ae.jpg)  
  
  
The new MSVC 14.3 build (Visual Studio 2022) failed, but so did the existing MSVC 14.2 build (Visual Studio 2019). Unfortunately, that does not really give us any idea whether this new build is any good. So technically, this PR replaces a build that refuses to start due to removed build environment with a failing build on a newer build environment. But at least it's one step towards testing with newer MSVC version.  
  
I am not really sure whether this is an improvement, though.  
@mloskot: If you feel that it's not, then just feel free to close this PR without merging it. It's probably time that somebody looked into the build failures with MSVC more closely, but unfortunately I do neither have the time nor the proper development environment to do this at the moment.

---

## Comment 2

> Username: mloskot  
> Created_at: 2022-04-09 10:01:43 UTC  
> Url: https://github.com/boostorg/gil/pull/644#issuecomment-1093844946  

> The new MSVC 14.3 build (Visual Studio 2022) failed, but so did the existing MSVC 14.2 build (Visual Studio 2019).  
  
I've just pushed this https://github.com/boostorg/gil/commit/cf1e6b11ba73d50e60fd26b0d84ec95031ab3672 which hopefully fix the failures (as far as I've checked them). Please, update your PR and let's see if it helps.  
  
> It's probably time that somebody looked into the build failures with MSVC more closely, but unfortunately I do neither have the time nor the proper development environment to do this at the moment.  
  
No worries, I will look into the build/test failures. Your CI improvements are very helpful, even if not complete as *Every Little Helps* :)

---

## Review 3 [Approved]

> Username: mloskot  
> Created_at: 2022-04-09 11:26:27 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/644#pullrequestreview-937120609  

I think this is fine to merge w/o updating.

---
