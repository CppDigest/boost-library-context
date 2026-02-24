# #95 Add support for build2 [Open]

> Username: boris-kolpackov  
> Created at: 2019-01-17 12:59:13 UTC  
> Updated at: 2019-03-07 10:01:45 UTC  
> Url: https://github.com/boostorg/predef/pull/95  

This patch make the library a `build2` package. It includes support for building and running tests. CI results:  
  
https://ci.stage.build2.org/?builds=libboost-predef  
  
Notes:  
  
- Using `build2` version from stage to take advantage of the latest features and fixes (the release is imminent).  
  
- Package/library naming follows Debian packages (if changing, do it in manifest and `build/bootstrap.build`).  
  
- Manifest may need tweaking (`url`, `email`, etc).  
  
- Only minimum `.gitignore` entries. Can add the full set to support in-source builds.  
  
If `build2` were the only build system...  
  
- We could auto-generate the version headers with `manifest` becoming the only place where the version needs updating.  
  
Let me know if there are any issues.

---

## Comment 1

> Username: boris-kolpackov  
> Created_at: 2019-02-22 14:12:04 UTC  
> Url: https://github.com/boostorg/predef/pull/95#issuecomment-466409021  

`build2` 0.9.0 is out so this now works with the latest released version. Here are the CI results:  
  
https://ci.cppget.org/?builds=libboost-predef

---

## Comment 2

> Username: hazelnusse  
> Created_at: 2019-03-07 04:35:11 UTC  
> Url: https://github.com/boostorg/predef/pull/95#issuecomment-470381333  

@boris-kolpackov FYI, I see no CI results at the URL you provided.

---

## Comment 3

> Username: boris-kolpackov  
> Created_at: 2019-03-07 10:01:45 UTC  
> Url: https://github.com/boostorg/predef/pull/95#issuecomment-470462522  

@hazelnusse Ah, they must have expired. Just re-submitted a new CI request, thanks for the heads-up.

---
