# #248 - AppVeyor builds are failing (for 9+ months), cygwin build issues exist [Open]

> Username: jeking3  
> Created at: 2018-11-11 22:32:00 UTC  
> Updated at: 2019-05-09 19:57:55 UTC  
> Url: https://github.com/boostorg/thread/issues/248  

Ran into a cygwin build issue when resolving issues in Boost.Regex over here:  
https://github.com/boostorg/regex/issues/64  
  
I took a look at the CI environment for Boost.Thread and it looks like the AppVeyor environment hasn't passed a build in a year.  Continuous Integration does not work if the results of the builds are ignored.

---

## Comment 1

> Username: viboes  
> Created at: 2018-11-12 18:32:13 UTC  
> Url: https://github.com/boostorg/thread/issues/248#issuecomment-437984180  

Yes, I know. I tried to setup them lately, without success based on the setup of Boost.System.  
Any whelp is more than welcome.  
I don't have a Windows machine at hand.

---

## Comment 2

> Username: jeking3  
> Created at: 2018-11-14 20:06:26 UTC  
> Url: https://github.com/boostorg/thread/issues/248#issuecomment-438800185  

If you are maintaining Boost.Thread you should update the meta/ directory contents and add your name to the list of maintainers.  
  
The PR you made a year ago to cut down the number of travis and appveyor jobs really trimmed things back - no builds for gcc < 7.3 (except whatever the default is for travis)?  no clang 5 or 6, and no analysis tools.  
  
With your permission, I'd like to ready a PR that will align the travis and appveyor jobs to the current CMT suite, documented at https://github.com/boostorg/boost-ci (scroll to the end of the README to see the jobs), and then prune them back carefully to find a balance between coverage and build time.  I see the build times are already fairly long, so I will analyze that as well to see if there's anything obvious that can be adjusted but still maintain the same quality of test.

---

## Comment 3

> Username: viboes  
> Created at: 2018-11-15 05:42:54 UTC  
> Url: https://github.com/boostorg/thread/issues/248#issuecomment-438924784  

Hi, thanks for your interest.  
  
The current state of the meta files contains my name as author and maintainer.  
BTW,  I should remove Niall as he is not maintaining anymore Boost.Thread.  
  
I'm open to any PR, and in particular to the good setup of travis and appveyor jobs.  
But, I would prefer to have a appveyor setup first that fixes the cygwin issue.  
BTW, what about CircleCi?  
  
The current tests take a lot of time as Boost.Thread uses real time to test the time related facilities. This is a mess as the jobs are not always gree due to this fact and I have to fix it, but I've not taken the time to work on it lately. Some ideas:  
* I was wondering if disabling (via a macro) all the unstable time related tests from these jobs was a good idea. We could execute them by the Boost regressions testers or explicitly.  
* Reorganize the whole ticket tests so that they are represented by regression tests  
* I have not think yet about alternative ways to test the time related features without using real time, but some kind of clock doubles. If anyone has an idea, please share. I should take a look at what the standard implementation are doing.  
  
I'm all for having coverall and coverity setups. Please, provide separated PR.  
  
Thanks in advance for your collaboration.

---

## Comment 4

> Username: jeking3  
> Created at: 2018-11-15 13:15:46 UTC  
> Url: https://github.com/boostorg/thread/issues/248#issuecomment-439036210  

I'd contact @ned14 directly about that our of courtesy, however having the list be accurate with the original author and the current maintainer(s) is useful.  
  
Regarding CircleCI I have not used it.  I have set up at least 20 Boost repositories with good CI coverage for Travis CI and for AppVeyor.  
  
It sounds like there should be opportunities to improve the Boost.Thread unit testing and maintain the same level of testing.  It would be possible to mock time, for example, if it is causing lengthy delays in test.  I haven't looked at the tests however, that's just from experience.  Unit tests should not have to block for real time.  Integration tests, sure, you want them using reality, but I would assume not every unit test needs to be an integration test.  
  
In other repositories (like Boost.Uuid) I have some jobs define `BOOST_NO_STRESS_TEST`, for example on the UBSAN and valgrind jobs, and that in turn reduces the loop count on some tests or benchmark executables so they execute reasonably fast, but still get the benefit of those tools.  It would be possible to have most of the build jobs in CI run unit tests and have a couple run integration tests.  If they ran in parallel (2 integration tests up front, and then 12 unit tests, and 4 more analysis tests) then they might all complete at about the same time with 5 concurrent build jobs... but not on AppVeyor since we get only 1 job in the free tier.  
  
I'll get started as soon as I can and focus on getting the AppVeyor tests passing, which is more important than getting them running faster.

---

## Comment 5

> Username: jeking3  
> Created at: 2018-11-20 19:16:46 UTC  
> Url: https://github.com/boostorg/thread/issues/248#issuecomment-440397109  

Making progress towards resolving this issue:  
https://ci.appveyor.com/project/jeking3/thread/builds/20434396

---

## Comment 6

> Username: viboes  
> Created at: 2018-11-23 06:08:46 UTC  
> Url: https://github.com/boostorg/thread/issues/248#issuecomment-441162024  

Aside trying to setup a cygwin setup fro appveyor, you added CI framework has already some added value.  
Please could you summarize it, so that we can decide if we can concentrate first on adopting the CI framework and then on fixing cygwin?

---

## Comment 7

> Username: viboes  
> Created at: 2019-05-09 19:19:02 UTC  
> Url: https://github.com/boostorg/thread/issues/248#issuecomment-491032037  

Is this working now?

---

## Comment 8

> Username: jeking3  
> Created at: 2019-05-09 19:57:55 UTC  
> Url: https://github.com/boostorg/thread/issues/248#issuecomment-491043915  

It's possible https://github.com/boostorg/thread/pull/263 fixed this, I will respin it.
