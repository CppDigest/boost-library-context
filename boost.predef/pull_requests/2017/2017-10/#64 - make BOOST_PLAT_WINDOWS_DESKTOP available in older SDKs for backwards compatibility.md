# #64 make BOOST_PLAT_WINDOWS_DESKTOP available in older SDKs for backwards compatibility [Merged]

> Username: jeking3  
> Created at: 2017-10-15 02:02:44 UTC  
> Updated at: 2017-10-18 12:00:03 UTC  
> Merged at: 2017-10-18 12:00:03 UTC  
> Closed at: 2017-10-18 12:00:03 UTC  
> Url: https://github.com/boostorg/predef/pull/64  

Also fixed up some of the documentation issues in the new UWP macros.

---

## Comment 1

> Username: jeking3  
> Created_at: 2017-10-17 14:50:36 UTC  
> Url: https://github.com/boostorg/predef/pull/64#issuecomment-337257325  

@pdimov @Lastique please review.

---

## Comment 2

> Username: Lastique  
> Created_at: 2017-10-17 16:02:46 UTC  
> Url: https://github.com/boostorg/predef/pull/64#issuecomment-337280714  

I have no comments wrt. implementation. Aren't docs need to be updated?

---

## Comment 3

> Username: pdimov  
> Created_at: 2017-10-17 16:12:08 UTC  
> Url: https://github.com/boostorg/predef/pull/64#issuecomment-337283391  

It looks good to me, except that I still think it's not deprecated.

---

## Comment 4

> Username: jeking3  
> Created_at: 2017-10-17 17:43:56 UTC  
> Url: https://github.com/boostorg/predef/pull/64#issuecomment-337310723  

@Lastique I thought I got everything, what did I miss?

---

## Comment 5

> Username: Lastique  
> Created_at: 2017-10-17 18:12:38 UTC  
> Url: https://github.com/boostorg/predef/pull/64#issuecomment-337319770  

@jeking3 I don't know, I just noticed there are QuickBook files and those are not updated by this PR.

---

## Comment 6

> Username: jeking3  
> Created_at: 2017-10-17 18:21:06 UTC  
> Url: https://github.com/boostorg/predef/pull/64#issuecomment-337322285  

@Lastique My impression is that the documentation is built from the comments in the source in these cases, which were updated.    
  
@pdimov There is only one mention of deprecation in the unit test, it is no longer in the header for desktop, so I will remove it from the unit test comment and it won't be deprecated any more.

---

## Comment 7

> Username: Lastique  
> Created_at: 2017-10-17 18:22:13 UTC  
> Url: https://github.com/boostorg/predef/pull/64#issuecomment-337322663  

> My impression is that the documentation is built from the comments in the source in these cases, which were updated.  
  
Oh, ok, thanks.

---

## Comment 8

> Username: jeking3  
> Created_at: 2017-10-17 18:26:16 UTC  
> Url: https://github.com/boostorg/predef/pull/64#issuecomment-337323872  

Once the current build completes hopefully all of the concerns are resolved.  I did leave `BOOST_PLAT_WINDOWS_RUNTIME` deprecated because it is incorrect and does not line up with a single `WINAPI_FAMILY` so it needs to be removed in the future.  `BOOST_PLAT_WINDOWS_DESKTOP` being available in older SDKs is no longer marked deprecated.

---

## Comment 9

> Username: pdimov  
> Created_at: 2017-10-17 18:31:07 UTC  
> Updated_at: 2017-10-17 18:31:20 UTC  
> Url: https://github.com/boostorg/predef/pull/64#issuecomment-337325466  

I can confirm that the documentation reflects the changes. I get one Quickbook warning,  
  
```  
doc\predef.qbk: warning: Invalid attributes for 'article document info': purpose  
, category, dirname  
```  
  
but that probably has nothing to do with this patch.

---

## Comment 10

> Username: pdimov  
> Created_at: 2017-10-17 18:33:02 UTC  
> Url: https://github.com/boostorg/predef/pull/64#issuecomment-337326020  

The tests pass for msvc-8.0, msvc-10.0, msvc-11.0, msvc-12.0, msvc-14.0, msvc-14.1. This looks good to go.

---
