# #157 add callable_traits submodule [Closed]

> Username: badair  
> Created at: 2017-07-30 20:46:50 UTC  
> Updated at: 2017-08-04 17:18:32 UTC  
> Closed at: 2017-08-04 17:18:31 UTC  
> Url: https://github.com/boostorg/boost/pull/157  



---

## Comment 1

> Username: ldionne  
> Created_at: 2017-07-30 21:09:00 UTC  
> Url: https://github.com/boostorg/boost/pull/157#issuecomment-318929794  

@pdimov @danieljames Is it necessary to submit a PR against `master` as well? We never merge `develop` into `master`, do we?

---

## Comment 2

> Username: danieljames  
> Created_at: 2017-07-30 21:19:40 UTC  
> Url: https://github.com/boostorg/boost/pull/157#issuecomment-318930478  

I normally just cherry-pick the changes into master. Although `.gitmodules` is inconsistent between branches, so there's sometimes some faffing around to get it to work.  
  
Regarding this pull request. there's no need to manually update `maintainers.txt`, it's automatically updated from the library metadata. Also, this pull request doesn't seem to add the actual submodule, at least I can't see it at https://github.com/badair/boost/tree/282bd4ee065177749f3934b9ed3c03db1f7c0f8a/libs. It's best to use `git submodule add` to add the submodule.

---

## Comment 3

> Username: pdimov  
> Created_at: 2017-07-30 21:43:52 UTC  
> Updated_at: 2017-07-30 21:44:21 UTC  
> Url: https://github.com/boostorg/boost/pull/157#issuecomment-318931759  

We won't add the submodule to `master` until after 1.65 anyway.  
  
Re the inconsistency, I think that we should fix it, or if not all of it (the order), at least use consistent submodule names on the two branches (because this results in redundant submodule updates when switching branches.)

---

## Comment 4

> Username: badair  
> Created_at: 2017-07-30 22:11:52 UTC  
> Url: https://github.com/boostorg/boost/pull/157#issuecomment-318933500  

@danieljames Thanks, I'll rework this PR.

---

## Comment 5

> Username: pdimov  
> Created_at: 2017-08-04 07:20:30 UTC  
> Url: https://github.com/boostorg/boost/pull/157#issuecomment-320176838  

@badair `--name` should be `callable_traits` and the URL should be `../callable_traits.git`; I'll add it manually, but first I'll fix the test and doc Jamfiles as they don't seem quite right to me.

---

## Comment 6

> Username: pdimov  
> Created_at: 2017-08-04 07:25:44 UTC  
> Url: https://github.com/boostorg/boost/pull/157#issuecomment-320177753  

Yeah, the .jam files in the repo root, as well as `boost.png` there, don't seem right either.

---

## Comment 7

> Username: pdimov  
> Created_at: 2017-08-04 07:50:47 UTC  
> Url: https://github.com/boostorg/boost/pull/157#issuecomment-320182404  

Oh great, your `include` directory is wrong too.

---

## Comment 8

> Username: pdimov  
> Created_at: 2017-08-04 08:05:45 UTC  
> Url: https://github.com/boostorg/boost/pull/157#issuecomment-320185323  

I started cleaning it up, see https://github.com/boostorg/callable_traits/commits/pdimov-develop-fixes, but encountered the `include` issue and had to abort. Your headers need to be in `include/boost/callable_traits`, not in `include/callable_traits`; consequently, all includes need to be `<boost/callable_traits/foo.hpp>`, not `<callable_traits/foo.hpp>`. You *have* to fix this first, there's no way to proceed otherwise.  
  
Also, your `develop` appears merged to `master`, but they differ significantly, as `git diff master..develop` shows. Would be a good idea to fix that too, as you're the only one who knows which of the two is authoritative when they disagree.

---

## Comment 9

> Username: badair  
> Created_at: 2017-08-04 15:34:25 UTC  
> Url: https://github.com/boostorg/boost/pull/157#issuecomment-320280143  

@pdimov I think the include dir is correct on the master branch (see [here](https://github.com/boostorg/callable_traits/tree/master/include/boost)), but the develop branch is super outdated. I will fix these issues when I return from vacation. Thanks for the commits.

---

## Comment 10

> Username: pdimov  
> Created_at: 2017-08-04 17:18:31 UTC  
> Url: https://github.com/boostorg/boost/pull/157#issuecomment-320304668  

OK, let us know when you're ready. To add the submodule to the develop branch of the superproject, your develop branch needs to be in order, so this PR isn't actionable at the moment.

---
