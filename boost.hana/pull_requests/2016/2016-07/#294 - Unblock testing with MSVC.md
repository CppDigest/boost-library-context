# #294 Unblock testing with MSVC [Closed]

> Username: MarcelRaad  
> Created at: 2016-07-08 21:25:15 UTC  
> Updated at: 2016-08-31 04:56:15 UTC  
> Closed at: 2016-08-31 02:33:20 UTC  
> Url: https://github.com/boostorg/hana/pull/294  

MSVC doesn't like the #warning directive and issues a fatal error.  
It also sets _MSVC_LANG to 201402 for C++14 instead of __cplusplus, but that's the minimum value anyway.  
It also doesn't like using {} instead of ::value for some type traits sometimes and issues an error about a missing default constructor. ::value is used most of the time anyway.

---

## Comment 1

> Username: ldionne  
> Created_at: 2016-07-09 23:35:01 UTC  
> Url: https://github.com/boostorg/hana/pull/294#issuecomment-231561637  

Do you mean that Hana's tests actually work on MSVC with this pull request? Which version of MSVC are you using?

---

## Comment 2

> Username: MarcelRaad  
> Created_at: 2016-07-10 07:24:35 UTC  
> Url: https://github.com/boostorg/hana/pull/294#issuecomment-231575064  

A few work, but unfortunately most of them don't. That's at least blocked by C++14 constexpr and a few bugs related to decltype in MSVC 14.3.  
  
But it's a bit unfortunate that all of them currently fail because of the #warning directive rather than compiler bugs as MSVC could be fully standards-conformant in a few months (see for example Andrew Pardoe's comments in https://www.reddit.com/r/cpp/comments/4r8n41/what_are_some_reasons_why_visual_studio_seems_so/).

---

## Comment 3

> Username: ldionne  
> Created_at: 2016-07-11 05:01:44 UTC  
> Updated_at: 2016-07-11 07:09:23 UTC  
> Url: https://github.com/boostorg/hana/pull/294#discussion_r70202505  

Why not simply `#if (__cplusplus < 201400) && !defined(_MSC_VER)`? And when `#warning` is not supported, we should fallback to `#pragma message` instead. Giving this message is important to avoid users being totally confused by meaningless error messages when trying to use Hana with a non-compliant compiler.

---

## Comment 4

> Username: MarcelRaad  
> Created_at: 2016-07-11 07:13:42 UTC  
> Url: https://github.com/boostorg/hana/pull/294#discussion_r70210474  

> Why not simply #if (__cplusplus < 201400) && !defined(_MSC_VER) ?  
  
Because I didn't know that clang in MSVC mode also supports `#pragma message`. But it does, just tested with clang/c2, so I've updated to use `#pragma message` in MSVC mode.

---

## Comment 5

> Username: ldionne  
> Created_at: 2016-07-12 02:05:50 UTC  
> Url: https://github.com/boostorg/hana/pull/294#issuecomment-231918291  

By any chance, do you think it would be possible to setup Appveyor builds for MSVC (while allowing failures) so that we could track down its progression?

---

## Comment 6

> Username: ldionne  
> Created_at: 2016-07-12 02:06:43 UTC  
> Url: https://github.com/boostorg/hana/pull/294#issuecomment-231918383  

It would also make this PR more concretely useful, because right now I personally have no way to check the correctness/usefulness of the PR; I can only blindly trust that it makes things better on MSVC.

---

## Comment 7

> Username: MarcelRaad  
> Created_at: 2016-07-12 17:27:13 UTC  
> Url: https://github.com/boostorg/hana/pull/294#issuecomment-232117861  

> By any chance, do you think it would be possible to setup Appveyor builds for MSVC (while allowing failures) so that we could track down its progression?  
>   
> It would also make this PR more concretely useful, because right now I personally have no way to check the correctness/usefulness of the PR; I can only blindly trust that it makes things better on MSVC.  
  
I fully agree. Unfortunately I have no idea about configuring AppVeyor builds using build scripts instead of Visual Studio project files, sorry :-(

---

## Comment 8

> Username: ldionne  
> Created_at: 2016-08-31 02:33:20 UTC  
> Url: https://github.com/boostorg/hana/pull/294#issuecomment-243641335  

Merged in 1b005c5679f5547dcdea3342dcb47ea5c54b9436, sorry for the long wait.

---

## Comment 9

> Username: MarcelRaad  
> Created_at: 2016-08-31 04:56:10 UTC  
> Url: https://github.com/boostorg/hana/pull/294#issuecomment-243658357  

Thanks!

---
