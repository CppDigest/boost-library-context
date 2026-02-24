# #334 - Travis Going Away - Need CI Replacement [Closed]

> Username: teeks99  
> Created at: 2020-12-17 03:21:46 UTC  
> Updated at: 2021-04-17 21:17:15 UTC  
> Closed at: 2021-04-17 21:17:15 UTC  
> Url: https://github.com/boostorg/python/issues/334  

It looks like travis-ci.org [is going to get a lot more restricted](https://www.jeffgeerling.com/blog/2020/travis-cis-new-pricing-plan-threw-wrench-my-open-source-works) for open source projects. At the end of the month, it will have to be moved to travis-ci.com at the very least.

---

## Comment 1

> Username: teeks99  
> Created at: 2020-12-17 03:34:48 UTC  
> Updated at: 2020-12-17 03:34:59 UTC  
> Url: https://github.com/boostorg/python/issues/334#issuecomment-747182980  

I'd like to propose moving to Github Actions, a lot of other people who were previously using Travis are heading there.   
  
Towards that end, I'm submitting pull request #335 . This is only a start (hence opening this issue to track to completion). I attempted to match the eight tests that are currently running on linux, without allowed failures. This doesn't include Mac OS (but it could) nor does it include pypy or documentation. Trying to walk before running.  
  
I had attempted to use Github's ubuntu-latest (18.04) or ubuntu-20.04 images for this, but wasn't able to get it working. Too many issues with installed versions of python and `ld` (see [this branch](https://github.com/teeks99/python/tree/gh-actions-test-plain)). Instead, I made [docker images](https://github.com/teeks99/boost-python-test-docker) ([docker hub](https://hub.docker.com/r/teeks99/boost-python-test)) for gcc and clang that are basically a more controlled way of organizing the tools and doing the caching that was done in travis.

---

## Comment 2

> Username: teeks99  
> Created at: 2020-12-17 03:39:29 UTC  
> Url: https://github.com/boostorg/python/issues/334#issuecomment-747184303  

There are [a bunch](https://github.com/ripienaar/free-for-dev#ci-and-cd) of other CI services that are worth investigating. Notable among them (and ones I've used or at least played with, and can recommend) are [CircleCI](https://circleci.com/) and [Azure Pipelines](https://azure.microsoft.com/en-us/services/devops/pipelines/).

---

## Comment 3

> Username: teeks99  
> Created at: 2020-12-17 12:06:56 UTC  
> Url: https://github.com/boostorg/python/issues/334#issuecomment-747400198  

The current travis files depend on boost 1.66 for testing. This seems rather old, is this the last one that supported C++98? Would it make sense to move to the latest version (currently 1.75)? Should we be testing against boostorg/boost master/develop branches?

---

## Comment 4

> Username: stefanseefeld  
> Created at: 2020-12-17 13:11:13 UTC  
> Url: https://github.com/boostorg/python/issues/334#issuecomment-747430154  

Boost.Python's dependency on the rest of Boost is rather limited, so I haven't taken the time to "upgrade" to a more recent Boost version. But if we move away from Travis-CI, we can of course change all that.

---

## Comment 5

> Username: stefanseefeld  
> Created at: 2020-12-18 01:02:06 UTC  
> Url: https://github.com/boostorg/python/issues/334#issuecomment-747801308  

Thank @teeks99 for raising the issue with Travis-CI. I'm looking into github actions right now, trying to figure out how to migrate. If you have suggestions or want to help, I'd be happy to hear it.

---

## Comment 6

> Username: teeks99  
> Created at: 2020-12-18 01:34:24 UTC  
> Url: https://github.com/boostorg/python/issues/334#issuecomment-747811038  

Yeah, I've gotten most of it setup in the pull request that I mentioned above. Did you get to look at that?

---

## Comment 7

> Username: teeks99  
> Created at: 2020-12-19 01:19:02 UTC  
> Url: https://github.com/boostorg/python/issues/334#issuecomment-748396728  

If we're going to include a boost version in the docker images, I'm thinking we should put that in the tag....I'll update the docker stuff to include that.  
  
Any interest in running against more compiler versionss (I keep a good stable of gcc/clang versions [here](https://hub.docker.com/r/teeks99/boost-cpp-docker/tags?page=1&ordering=-name), easy to add)?

---

## Comment 8

> Username: teeks99  
> Created at: 2020-12-19 01:19:51 UTC  
> Url: https://github.com/boostorg/python/issues/334#issuecomment-748396856  

How about interest in running against other c++ std's?

---

## Comment 9

> Username: stefanseefeld  
> Created at: 2020-12-19 02:02:10 UTC  
> Url: https://github.com/boostorg/python/issues/334#issuecomment-748402379  

It's good to have such build variants available (compilers, C++ standards), but I think it would be wasteful to run all of those on each commit / PR.

---

## Comment 10

> Username: teeks99  
> Created at: 2020-12-19 02:16:20 UTC  
> Url: https://github.com/boostorg/python/issues/334#issuecomment-748404170  

Maybe nightly against master/develop?

---

## Comment 11

> Username: stefanseefeld  
> Created at: 2020-12-19 02:50:39 UTC  
> Url: https://github.com/boostorg/python/issues/334#issuecomment-748408569  

...or even weekly, yes. (I need to familiarize myself with what schedulers exist with github actions.)

---

## Comment 12

> Username: teeks99  
> Created at: 2020-12-19 12:47:00 UTC  
> Url: https://github.com/boostorg/python/issues/334#issuecomment-748470085  

Instead of having a workflow that is triggered by a push/pull request, you can have it [triggered by cron](https://docs.github.com/en/free-pro-team@latest/actions/reference/events-that-trigger-workflows#scheduled-events).

---

## Comment 13

> Username: teeks99  
> Created at: 2020-12-19 12:50:10 UTC  
> Url: https://github.com/boostorg/python/issues/334#issuecomment-748470432  

I've got a [branch](https://github.com/teeks99/python/tree/github-actions-test-mac) going with an attempt at support for mac, but it is failing to find `boost/config.hpp` or `build/src/clang++-12.0.0/x86_64/shared/libboost_python27.so`. I'm not fluent in Faber, is there something that needs to be setup for these? I'm especially confused about the first one as I tried to keep it very close to what *is* working on travis.

---

## Comment 14

> Username: stefanseefeld  
> Created at: 2021-04-17 21:17:15 UTC  
> Url: https://github.com/boostorg/python/issues/334#issuecomment-821888947  

The transition away from Travis-CI is completed.
