# #739 ci: update actions/checkout in GitHub Actions workflows to v4 [Merged]

> Username: striezel  
> Created at: 2023-12-23 10:32:14 UTC  
> Updated at: 2025-08-03 18:10:35 UTC  
> Merged at: 2023-12-24 23:01:54 UTC  
> Closed at: 2023-12-24 23:01:54 UTC  
> Url: https://github.com/boostorg/gil/pull/739  

### Description  
  
This updates [`actions/checkout`](https://github.com/actions/checkout) to v4, it's current version.  
  
Changelog:  
  
> ## v4.1.0  
> - Add support for partial checkout filters  
>  
> ## v4.0.0  
> - Support fetching without the --progress option  
> - Update to node20  
  
### Tasklist  
  
- [x] Ensure all CI builds pass  
- [x] Review and approve

---

## Comment 1

> Username: striezel  
> Created_at: 2023-12-23 10:37:28 UTC  
> Url: https://github.com/boostorg/gil/pull/739#issuecomment-1868265221  

This seems to fail on all the jobs using the `ubuntu:18.04` Docker container image. :(

---

## Comment 2

> Username: mloskot  
> Created_at: 2023-12-23 19:12:59 UTC  
> Url: https://github.com/boostorg/gil/pull/739#issuecomment-1868353974  

Yeah, this looks like a known issue for Ubuntu 18.04 which is incompatible with Node required by the checkout v4 action:  
- https://github.com/actions/checkout/issues/1442#issuecomment-1697949642  
- https://github.com/actions/checkout/issues/1474  
  
I can't see what can be done about it, other than postpone v3 to v4 pgrade or remove the jobs based on Ubuntu 18.04 (losing GCC 6, Clang 3.9, 4.0 and 5.0).

---

## Comment 3

> Username: striezel  
> Created_at: 2023-12-24 18:34:16 UTC  
> Url: https://github.com/boostorg/gil/pull/739#issuecomment-1868573521  

> I can't see what can be done about it, other than postpone v3 to v4 pgrade or remove the jobs based on Ubuntu 18.04 (losing GCC 6, Clang 3.9, 4.0 and 5.0).  
  
I am not sure that losing those CI jobs is acceptable. So I decided not to update the workflows that contain containerized builds to to `actions/checkout@v4` and just update the others instead. It may not be the ideal solution, but it's better than nothing, and the jobs should pass now - again.

---

## Comment 4

> Username: mloskot  
> Created_at: 2023-12-24 18:45:02 UTC  
> Updated_at: 2023-12-24 18:45:16 UTC  
> Url: https://github.com/boostorg/gil/pull/739#issuecomment-1868575002  

Thank you @striezel   
  
Another workaround could be to split this `posix` job   
  
https://github.com/boostorg/gil/blob/e24c87fde24a4027bc7a0a5c873ff18c39097128/.github/workflows/ci.yml#L16-L24  
  
into `posix` and `posix-legacy`, and move 18.04-based builds to the later, then the `posix` could be upgraded to `checkout@v4`.  
  
This would mean copying some YAML boilerplate, but I personally don't die hard for DRY in YAML-s :)

---

## Comment 5

> Username: striezel  
> Created_at: 2023-12-24 18:56:49 UTC  
> Url: https://github.com/boostorg/gil/pull/739#issuecomment-1868576521  

That split would make the YAML file even longer and more complicated than it is now. I don't feel that `actions/checkout@v3` vs. `v4` is such a big improvement that it is worth the effort to make the CI configuration more complicated and maintain two copies of basically the same CI steps.

---

## Comment 6

> Username: mloskot  
> Created_at: 2023-12-24 22:59:21 UTC  
> Url: https://github.com/boostorg/gil/pull/739#issuecomment-1868607720  

Sure. By the way, I kind of steal that from Peter Dimov's https://github.com/boostorg/mp11/blob/develop/.github/workflows/ci.yml 😊

---

## Review 7 [Approved]

> Username: mloskot  
> Created_at: 2023-12-24 23:01:32 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/739#pullrequestreview-1795628948  

Thanks @striezel

---
