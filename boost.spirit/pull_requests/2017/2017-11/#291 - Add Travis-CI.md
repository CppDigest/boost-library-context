# #291 Add Travis-CI [Merged]

> Username: Kojoley  
> Created at: 2017-11-25 15:52:33 UTC  
> Updated at: 2017-11-28 14:45:38 UTC  
> Merged at: 2017-11-28 13:20:54 UTC  
> Closed at: 2017-11-28 13:20:54 UTC  
> Url: https://github.com/boostorg/spirit/pull/291  

I have chosen to run builds with `warnings-as-errors=on` to ensure that PRs bring no new warning.  
`warnings=on` still produces some warning, they are suppressed by `known-warnings=suppress`.   
I did not enable `warnings=all` because it requires more work while `warnings=on` is not yet completed.  
  
Clang builds faster than GCC, so I chose to bootstrap using `--with-toolset=clang`.  
  
I have place Boost libraries built as a separate step so it do not spoil the tests log and it will show a build error rather than a tests failure.  
  
Testing with `variant=release` because most (even if not all) the boost test runners use `variant=debug` (implicitly, I have figured it out while dealing bug in subrules; according to docs, without a `variant=` argument it should be `variant=release` by default on linux, but the runners show `-O0 -g` in build commands).  
  
Ccache is use to speed up the tests process. It should be safe to use it on the whole test suite as it tracks dependencies and it does not affect Boost.Build (objects/libraries/executables are always 'builded'/linked).  
  
GCC 7 is 4 times larger than GCC 6 and takes a lot of time (3 times slower than GCC 6) to install on Travis, so I have used it only to test X3.  
  
Clang builds uses `stdlib=libc++` because default `libstdc++` on Travis is ancient (installing a new one requires adding repos what significantly increases build times because causes updates of other stuff like default GCC) plus most boost regression test runners use `libstdc++`.  
  
Docs and header inclusion tests I am planing to place on Circle-CI, but it is not on my priority list by now.  
  
Running classic tests on CI is debatable and may be removed.

---

## Comment 1

> Username: djowel  
> Created_at: 2017-11-26 00:26:08 UTC  
> Url: https://github.com/boostorg/spirit/pull/291#issuecomment-346975315  

I am not yet familiar with Travis and the steps needed to activate it, but we have Travis for Fusion and Phoenix courtesy of @Flast.

---

## Comment 2

> Username: octopus-prime  
> Created_at: 2017-11-26 16:12:35 UTC  
> Url: https://github.com/boostorg/spirit/pull/291#issuecomment-347019490  

Nice job! Wanted to do the same...  
Here is the corresponding issue "travis and appveyor integration" #294

---

## Comment 3

> Username: octopus-prime  
> Created_at: 2017-11-27 10:19:30 UTC  
> Updated_at: 2017-11-27 10:27:21 UTC  
> Url: https://github.com/boostorg/spirit/pull/291#issuecomment-347138179  

- We should run tests on osx too (see #295)  
- I my opinion in this yml file is a lot of boilerplate to execute build jobs (e.g. sed into user-config.jam). Could be much easier? (see #295)  
- We should split build jobs into parts for (qi, karma, x3, ...) and add them to matrix. So we get faster build times (e.g. 5min for x3) instead of 30min for all-in-one-builds. I see it was done in yml file but the Jamfiles are reverted...

---

## Comment 4

> Username: octopus-prime  
> Created_at: 2017-11-27 10:22:51 UTC  
> Url: https://github.com/boostorg/spirit/pull/291#issuecomment-347139020  

Peter Dimov told me that spirit is activated in travis.

---

## Comment 5

> Username: octopus-prime  
> Created_at: 2017-11-28 13:56:21 UTC  
> Url: https://github.com/boostorg/spirit/pull/291#issuecomment-347530630  

Yes again - merge without discussion... :-1:

---

## Comment 6

> Username: Kojoley  
> Created_at: 2017-11-28 14:10:24 UTC  
> Url: https://github.com/boostorg/spirit/pull/291#issuecomment-347534481  

You had 3 days and posted comments here. So the discussion had taken place.

---

## Comment 7

> Username: octopus-prime  
> Created_at: 2017-11-28 14:29:33 UTC  
> Url: https://github.com/boostorg/spirit/pull/291#issuecomment-347540307  

There were comments.  
Discussion is bi-directional.  
Useless to discuss with me alone...

---

## Comment 8

> Username: Kojoley  
> Created_at: 2017-11-28 14:45:38 UTC  
> Url: https://github.com/boostorg/spirit/pull/291#issuecomment-347545537  

> There were comments.  
  
Let's look  
  
> We should run tests on osx too  
  
Spirit does not do anything os-specific. OSX runs Clang, we check it on Linux. Travis OSX capacity is very limited and runs on virtual machines (not in containers, so it is slow to startup) with single core.  
  
> I my opinion in this yml file is a lot of boilerplate to execute build jobs (e.g. sed into user-config.jam). Could be much easier?  
  
What should I say for it? Why it is written like this you were pointed (not by me) in #295 and there is the description in the PR.  
  
> We should split build jobs into parts for (qi, karma, x3, ...) and add them to matrix. So we get faster build times (e.g. 5min for x3) instead of 30min for all-in-one-builds. I see it was done in yml file but the Jamfiles are reverted...  
  
You again do not read the description, not the first time, I cannot force you to read though.  
  
Any other question?

---
