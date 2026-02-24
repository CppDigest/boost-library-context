# #24 Add CI framework [Merged]

> Username: jeking3  
> Created at: 2019-06-04 14:54:15 UTC  
> Updated at: 2019-06-05 23:05:36 UTC  
> Merged at: 2019-06-05 23:05:36 UTC  
> Closed at: 2019-06-05 23:05:36 UTC  
> Url: https://github.com/boostorg/contract/pull/24  

This pull requests integrates this code repository with various continuous integration tools.   
  
  - travis with valgrind, cppcheck, ubsan, codecov, covscan (future)  
  - appveyor with MSVC 2010 through 2017, cygwin 32/64, mingw 32/64  
  - README, LICENSE, etc.  
  
This is based on the work in boost-ci, found at https://github.com/boostorg/boost-ci.  This work aims to make code submissions higher quality and detect issues before code is submitted to `develop`.  This means authors, maintainers, bug fixers are encourages to submit pull requests for all their changes, and let the CI build verify they are good before merging.  
  
To take advantage of this a number of steps have to be taken by an admin on this repository to enable Travis CI and Appveyor.  Since these are not currently enabled on this repository, I built this pull request on my fork where it is integrated:  
  
AppVeyor: https://ci.appveyor.com/project/jeking3/contract/builds/25032326  
Travis CI: https://travis-ci.org/jeking3/contract/builds/541239458  
  
If you are interested I can help configure and maintain these tools for you, but it would require granting me admin access to set this up.  I am on the Boost CMT.  
  
There are a number of known failures; if these are never going to be fixed (like gcc 4.8 and 4.9) they can be removed from the build.  I already removed MSVC 2010 and 2012, but I left MSVC 2013 because it looks like a new issue.  Valgrind was fixed, but I guess that broke gcc 5 builds, so that was fixed and now valgrind is broken again.  The CI framework will help avoid these "whack a mole" scenarios if changes go in through pull requests.

---

## Comment 1

> Username: jeking3  
> Created_at: 2019-06-04 16:14:42 UTC  
> Url: https://github.com/boostorg/contract/pull/24#issuecomment-498740359  

Sorry, closed the wrong one!  Wanted to close my fork's PR.

---

## Comment 2

> Username: jeking3  
> Created_at: 2019-06-04 16:30:05 UTC  
> Url: https://github.com/boostorg/contract/pull/24#issuecomment-498746131  

Sorry, the closing and opening caused a duplicate build in the travis.com account; Build #2 is a duplicate of Build #3.  You can cancel Build #2 to free up some resources.

---

## Comment 3

> Username: codecov[bot]  
> Created_at: 2019-06-04 16:38:22 UTC  
> Updated_at: 2019-06-04 20:20:12 UTC  
> Url: https://github.com/boostorg/contract/pull/24#issuecomment-498749188  

# [Codecov](https://codecov.io/gh/boostorg/contract/pull/24?src=pr&el=h1) Report  
> :exclamation: No coverage uploaded for pull request base (`develop@eedf51b`). [Click here to learn what that means](https://docs.codecov.io/docs/error-reference#section-missing-base-commit).  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/contract/pull/24/graphs/tree.svg?width=650&token=EL686wMU6K&height=150&src=pr)](https://codecov.io/gh/boostorg/contract/pull/24?src=pr&el=tree)  
  
```diff  
@@            Coverage Diff             @@  
##             develop      #24   +/-   ##  
==========================================  
  Coverage           ?   63.07%             
==========================================  
  Files              ?       30             
  Lines              ?      715             
  Branches           ?      279             
==========================================  
  Hits               ?      451             
  Misses             ?      108             
  Partials           ?      156  
```  
  
  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/contract/pull/24?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/contract/pull/24?src=pr&el=footer). Last update [eedf51b...dc61522](https://codecov.io/gh/boostorg/contract/pull/24?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 4

> Username: lcaminiti  
> Created_at: 2019-06-04 22:06:38 UTC  
> Url: https://github.com/boostorg/contract/pull/24#issuecomment-498861288  

I looked at the pull request. This all new stuff to me... but it looks good. Before I merge it to develop, I would like to confirm:  
Will this pull request run CI on example/* or just test/*?

---

## Comment 5

> Username: jeking3  
> Created_at: 2019-06-05 02:08:55 UTC  
> Updated_at: 2019-06-05 02:09:15 UTC  
> Url: https://github.com/boostorg/contract/pull/24#issuecomment-498910986  

It runs b2 on the top level Jamfile and I disabled examples/ in there.  If you have any questions about how this works let me know, I'd be happy to expand on what's going on.

---
