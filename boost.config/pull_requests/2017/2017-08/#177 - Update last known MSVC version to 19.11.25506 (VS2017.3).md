# #177 Update last known MSVC version to 19.11.25506 (VS2017.3). [Merged]

> Username: gongminmin  
> Created at: 2017-08-15 04:26:22 UTC  
> Updated at: 2017-10-22 16:59:53 UTC  
> Merged at: 2017-08-15 16:54:16 UTC  
> Closed at: 2017-08-15 16:54:16 UTC  
> Url: https://github.com/boostorg/config/pull/177  

Update the version number to VS2017.3's.

---

## Comment 1

> Username: MarcelRaad  
> Created_at: 2017-08-15 06:40:59 UTC  
> Url: https://github.com/boostorg/config/pull/177#issuecomment-322389290  

According to https://blogs.msdn.microsoft.com/vcblog/2017/08/11/c17-features-and-stl-fixes-in-vs-2017-15-3, structured bindings are now supported, so `BOOST_NO_CXX17_STRUCTURED_BINDINGS` probably shouldn't be defined for `_MSC_VER` >= 1911?

---

## Review 2 [Approved]

> Username: DanielaE  
> Created_at: 2017-08-15 15:57:52 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/config/pull/177#pullrequestreview-56384658  

Works beautifully with my setup!

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2017-08-15 16:55:14 UTC  
> Url: https://github.com/boostorg/config/pull/177#issuecomment-322524428  

Merged, I'll deal with any new features and changed config macros later: unless someone would like to submit a PR of course :)

---

## Comment 4

> Username: KindDragon  
> Created_at: 2017-08-18 14:00:33 UTC  
> Url: https://github.com/boostorg/config/pull/177#issuecomment-323361787  

@MarcelRaad @jzmaddock Boost Config test `no_cxx17_structured_bindings` pass for VC2017U3

---

## Comment 5

> Username: MarcelRaad  
> Created_at: 2017-08-18 15:32:39 UTC  
> Url: https://github.com/boostorg/config/pull/177#issuecomment-323386030  

@KindDragon I've just tried this too today and created PR #179 :-)

---

## Comment 6

> Username: glenfe  
> Created_at: 2017-08-18 15:54:36 UTC  
> Url: https://github.com/boostorg/config/pull/177#issuecomment-323391612  

PR 179 looks good. Note that there's no rush to get it into 1.65 because the developer that I added BOOST_NO_CXX17_STRUCTURED_BINDINGS for has not yet even submitted his pull request that would make use of it. :)

---

## Comment 7

> Username: Croydon  
> Created_at: 2017-10-22 16:53:01 UTC  
> Url: https://github.com/boostorg/config/pull/177#issuecomment-338491944  

Sorry, for not having an overview here.  
  
What is/will be the first stable Boost release, which includes this pull request?

---

## Comment 8

> Username: jzmaddock  
> Created_at: 2017-10-22 16:59:53 UTC  
> Url: https://github.com/boostorg/config/pull/177#issuecomment-338492435  

It's in 1.65.1

---
