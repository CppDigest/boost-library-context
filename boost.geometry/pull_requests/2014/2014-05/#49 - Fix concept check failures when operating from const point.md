# #49 Fix concept check failures when operating from const point. [Closed]

> Username: smspillaz  
> Created at: 2014-05-29 01:51:04 UTC  
> Updated at: 2014-05-29 16:09:17 UTC  
> Closed at: 2014-05-29 16:09:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/49  

`arithmetic.hpp` had assertions to check that `Point2` (a `const` point) satisfied `concept::Point`, whereas that check should have been made on `Point1`.

---

## Comment 1

> Username: awulkiew  
> Created_at: 2014-05-29 11:55:11 UTC  
> Url: https://github.com/boostorg/geometry/pull/49#issuecomment-44524259  

I'll wait with the merge til the end of the day in case someone had any objections. For the future, please add some name of a library components which the commits modifies, in the square brackets, in this case e.g. [arithmetic][test].

---

## Comment 2

> Username: mkaravel  
> Created_at: 2014-05-29 11:56:41 UTC  
> Url: https://github.com/boostorg/geometry/pull/49#issuecomment-44524349  

FYI, Barend will be away until Sunday, so it might be a good idea to wait until then.

---

## Comment 3

> Username: awulkiew  
> Created_at: 2014-05-29 12:59:14 UTC  
> Url: https://github.com/boostorg/geometry/pull/49#issuecomment-44528575  

We should wait then. Besides, I have objections.  
  
First of all this branch was created from master and you request merging with the master branch. The code in the master branch is around 6 months behind develop branch.  
  
This is not how it should be done. Boost uses GitFlow workflow so branches containing fixes and new features should be created from develop branch and merged with the develop branch. See: https://github.com/boostorg/geometry/wiki/Contribution-Tutorial for more info.  
  
The easy way of fixing it would be to create a new branch from the latest develop in your fork, merge your geometry/10077 with it, run all the tests and then send a pull request.  
  
Another thing is that I just run tests on this branch and get failing geometries/boost_range.cpp test on Clang but this is probably not caused by this change.

---

## Comment 4

> Username: mkaravel  
> Created_at: 2014-05-29 13:04:59 UTC  
> Url: https://github.com/boostorg/geometry/pull/49#issuecomment-44529036  

Another way of doing things and possibly avoid any conflicts when merging geometry/10077 is to branch from develop and then cherry-pick the single commit of this pull request.  
  
I really do not know what is the best way of doing this though.

---

## Comment 5

> Username: smspillaz  
> Created_at: 2014-05-29 16:08:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/49#issuecomment-44549738  

Hey @awulkiew @mkaravel   
  
Thanks for the comments and instructions on the git-flow workflow. I was wondering why the history seemed so quiet.  
  
I had a look at the contribution guide (here)[https://svn.boost.org/trac/boost/wiki/StartModPatchAndPullReq], but there's no mention of the git-flow workflow, so I suspect that needs to be updated.  
  
In any event, I've now rebased this change on top of develop and all the tests are passing. I've also amended the commit message as requested.   
  
The failing `boost_range` test was failing before this change and is unrelated indeed.

---

## Comment 6

> Username: smspillaz  
> Created_at: 2014-05-29 16:09:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/49#issuecomment-44549798  

Looks like I'll have to close this PR and open another one though to change the target branch.

---
