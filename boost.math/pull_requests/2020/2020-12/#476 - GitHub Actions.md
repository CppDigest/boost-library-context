# #476 GitHub Actions [Merged]

> Username: mborland  
> Created at: 2020-12-28 16:34:11 UTC  
> Updated at: 2021-01-11 18:46:20 UTC  
> Merged at: 2021-01-11 14:53:19 UTC  
> Closed at: 2021-01-11 14:53:19 UTC  
> Url: https://github.com/boostorg/math/pull/476  

CI using GitHub Actions that conducts the current battery of tests (float128_tests, special_fun, distribution_tests, misc, quadrature, ../example//examples, ../tools) with the following toolchains:  
  
- GCC 9&10 and Clang 9&10 on Ubuntu 20.04  
- GCC 7&8 and Clang 7&8 on Ubuntu 18.04  
- GCC 5&6 and Clang 5&6 on Ubuntu 16.04  
- Apple Clang on latest provided MacOS  
- GCC and MSVC 14.2 on latest provided Windows  
  
I see two major advantages of using GitHub Actions for CI:  
  
1. The 350 tests this CI file starts complete in a fraction of the time (~6 hours) the current Travis+Appveyor setup takes while being more comprehensive.   
2. Anyone who forks this repository would have CI on their fork. This should significantly reduce the demand on the Boost CI account and maintainers.  
  
 This is heavily adapted from work done by @evanmiller. An example of the results can be found [here](https://github.com/mborland/math/actions/runs/448893348).

---

## Comment 1

> Username: evanmiller  
> Created_at: 2020-12-28 17:17:19 UTC  
> Url: https://github.com/boostorg/math/pull/476#issuecomment-751793957  

Very cool! I was going to wait until all the tests were passing under Actions before opening a PR (cf. the slew of issues opened 6 days ago). I'm happy to support this PR since you have added quite a bit to my initial YAML file.

---

## Comment 2

> Username: mborland  
> Created_at: 2020-12-29 12:00:11 UTC  
> Updated_at: 2020-12-29 12:11:09 UTC  
> Url: https://github.com/boostorg/math/pull/476#issuecomment-752051070  

I figured it would be useful to have to fix the slew of issues that you opened. Commit 773f583 lets you specify platform ([APPLE], [WINDOWS], or [LINUX] at the end of the commit message) which should speed things up given the preponderance of the errors were apple related.

---

## Comment 3

> Username: ckormanyos  
> Created_at: 2020-12-29 13:41:05 UTC  
> Url: https://github.com/boostorg/math/pull/476#issuecomment-752078398  

> CI using GitHub Actions that conducts the current battery of tests...  
  
Awesome! Idea Matt. I'm also in the process of learning the basics of GtiActions in another project domain.  
My open source projects are limited to something like 2,000 virtual server build minutes per month.  
Does boost have/would it require a paid plan? If the minutes needed are larger.

---

## Comment 4

> Username: evanmiller  
> Created_at: 2020-12-29 13:43:08 UTC  
> Url: https://github.com/boostorg/math/pull/476#issuecomment-752079094  

@ckormanyos My understanding is public repositories have unlimited minutes. The 2,000 minute figure applies to private repos.

---

## Comment 5

> Username: ckormanyos  
> Created_at: 2020-12-29 13:59:58 UTC  
> Url: https://github.com/boostorg/math/pull/476#issuecomment-752084453  

> The 2,000 minute figure applies to private repos.  
  
Ah yes. That's right for open source. Thank you. My mistake.

---

## Comment 6

> Username: jzmaddock  
> Created_at: 2020-12-29 18:47:39 UTC  
> Url: https://github.com/boostorg/math/pull/476#issuecomment-752201652  

Thanks guys!  
  
A couple of quick questions:  
  
1) What do we need to do to activate this so we can see the results of a run?  
2) Is it possible to test msvc-14.1 and 14.0 as well, or should we leave that to appveyor?  What about mingw?  
  
Thanks!

---

## Comment 7

> Username: mborland  
> Created_at: 2020-12-29 19:07:16 UTC  
> Updated_at: 2020-12-29 19:49:30 UTC  
> Url: https://github.com/boostorg/math/pull/476#issuecomment-752208242  

The results will generally look like [this](https://github.com/mborland/math/actions/runs/448893348). To enable actions on this repo merging this in should work but if not you can click the actions tab at the top, "setup a workflow yourself", then paste this file in. As for adding MSVC-14.1, MSVC-14.0 & Mingw? I'll have to try on my repo and report back.  
  
Edit: According to the run config info GCC on Windows is provided by mingw, so that is already covered.

---

## Comment 8

> Username: mborland  
> Created_at: 2020-12-30 13:26:50 UTC  
> Url: https://github.com/boostorg/math/pull/476#issuecomment-752597542  

@jzmaddock commit 50a52fa provides mingw, MSVC 14.0, and MSVC 14.2 under Windows. I didn't see anything in the docs or by trial and error that provides 14.1.

---

## Comment 9

> Username: ckormanyos  
> Created_at: 2020-12-30 13:40:53 UTC  
> Url: https://github.com/boostorg/math/pull/476#issuecomment-752616736  

> To enable actions on this repo  
  
I'm kind of new to this, but I found it simple to (correct me if this is wrong):  
- Make a branch.  
- Create a directory in that branch called .github/workflows (here the dot is actually part of the directory name).  
- Add the relevant YAML script into the workflows directory.  
- On checkin or whatever actions are easily seen near the top of the YAML code, the actions will be executed.

---

## Comment 10

> Username: evanmiller  
> Created_at: 2020-12-30 13:41:28 UTC  
> Url: https://github.com/boostorg/math/pull/476#issuecomment-752617426  

@mborland May want to throw a status badge into the README while you're at it  
  
```  
![CI](https://github.com/boostorg/math/workflows/CI/badge.svg?branch=develop)  
```

---

## Comment 11

> Username: mborland  
> Created_at: 2020-12-30 14:11:15 UTC  
> Url: https://github.com/boostorg/math/pull/476#issuecomment-752635380  

@ckormanyos That is correct.

---

## Comment 12

> Username: mborland  
> Created_at: 2021-01-10 17:52:17 UTC  
> Url: https://github.com/boostorg/math/pull/476#issuecomment-757515938  

@jzmaddock Is there anything in here that you want changed?

---

## Comment 13

> Username: mborland  
> Created_at: 2021-01-11 18:00:08 UTC  
> Url: https://github.com/boostorg/math/pull/476#issuecomment-758121977  

@jzmaddock first run took only 2h 8m 8s. It looks like the current one has more than 40 simultaneous tests going. I thought boost was a free account but clearly not [since free only allows 20.](https://docs.github.com/en/free-pro-team@latest/actions/reference/usage-limits-billing-and-administration)

---

## Comment 14

> Username: ckormanyos  
> Created_at: 2021-01-11 18:23:53 UTC  
> Url: https://github.com/boostorg/math/pull/476#issuecomment-758136080  

>  It looks like the current one has more than 40 simultaneous tests going. I thought boost was a free account but clearly not since free only allows 20.  
  
Is there any way, beyond supposition, to lucidly clear this up? I often feel insecure about firing up CI because in general my confidence is not so super-high in this domain. And not knowing if my CI might incur more or less charges makes me _even_ more timid to go for it.

---

## Comment 15

> Username: mborland  
> Created_at: 2021-01-11 18:43:03 UTC  
> Url: https://github.com/boostorg/math/pull/476#issuecomment-758146184  

@ckormanyos The pricing plan is [free for public repos](https://github.com/pricing) in all cases. I do not even have billing information on file with Github and it runs on my personal fork.

---

## Comment 16

> Username: ckormanyos  
> Created_at: 2021-01-11 18:46:20 UTC  
> Url: https://github.com/boostorg/math/pull/476#issuecomment-758148035  

> The pricing plan is free for public repos in all cases.  
  
Thank you.

---
