# #38 Remove linking with Boost.System [Merged]

> Username: Lastique  
> Created at: 2019-01-14 18:12:11 UTC  
> Updated at: 2021-09-30 11:59:39 UTC  
> Merged at: 2021-09-30 10:42:20 UTC  
> Closed at: 2021-09-30 10:42:20 UTC  
> Url: https://github.com/boostorg/locale/pull/38  

Since Boost.System is header-only now, no need to link with the library.  
  
Also, trim trailing spaces in the jamfile.

---

## Comment 1

> Username: hdu-sdlzx  
> Created_at: 2021-09-29 14:26:02 UTC  
> Url: https://github.com/boostorg/locale/pull/38#issuecomment-930228982  

Will anyone review and merge this PR? @pdimov

---

## Review 2 [Approved]

> Username: Flamefire  
> Created_at: 2021-09-29 16:14:13 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/locale/pull/38#pullrequestreview-766916286  

LGTM

---

## Comment 3

> Username: hdu-sdlzx  
> Created_at: 2021-09-30 08:16:24 UTC  
> Url: https://github.com/boostorg/locale/pull/38#issuecomment-931003804  

ping @artyom-beilis

---

## Comment 4

> Username: artyom-beilis  
> Created_at: 2021-09-30 10:49:38 UTC  
> Updated_at: 2021-09-30 10:50:51 UTC  
> Url: https://github.com/boostorg/locale/pull/38#issuecomment-931210319  

> ping @artyom-beilis  
  
Isn't `boost_system` required by `boost_thread` - I found this changeset: ef9d900a5ac6844a07498520961368ed2163efc8 that I explicitly added system. I don't recall what was the case maybe some static linking that boost_thread didn't broght system, I don't really remember.  
  
Anycase we will see if something fails on tests :-)

---

## Comment 5

> Username: Flamefire  
> Created_at: 2021-09-30 10:51:52 UTC  
> Url: https://github.com/boostorg/locale/pull/38#issuecomment-931211737  

> Anycase we will see if something fails on tests :-)  
  
Preparing CI tests ATM. But as we don't use Boost.System ourselves and it is really header-only now linking to it should no longer be required

---

## Comment 6

> Username: artyom-beilis  
> Created_at: 2021-09-30 10:57:33 UTC  
> Url: https://github.com/boostorg/locale/pull/38#issuecomment-931215368  

> it is really header-only now linking to it should no longer be required  
  
Ahhhh... missed that! thanks a lot!

---
