# #72 - Merging 'develop' changes to 'master' [Closed]

> Username: eldiener  
> Created at: 2019-01-30 21:21:11 UTC  
> Updated at: 2021-12-08 15:47:16 UTC  
> Closed at: 2021-12-08 15:47:15 UTC  
> Url: https://github.com/boostorg/parameter/issues/72  

I would like to move forward with merging the 'develop' changes which Cromwell Enage has been making to improve the Parameter library to the 'master' branch, so that those changes can be part of the next release of Boost.  
  
With what other Boost libraries do we need to sync in order to merge Parameter 'develop' to 'master' ? As I understand it we probably need to sync this merge with Accumulators, Log, and Convert. Are there other libraries with which we need to sync the changes as we go to the 'master' branch ?

---

## Comment 1

> Username: CromwellEnage  
> Created at: 2019-02-01 15:28:57 UTC  
> Url: https://github.com/boostorg/parameter/issues/72#issuecomment-459760303  

Lockfree and Signals2 also need to be synced since they rely on BOOST_PARAMETER_TEMPLATE_KEYWORD, which was moved to <boost/parameter/template_keyword.hpp>.  Graph also needs to be synced.

---

## Comment 2

> Username: CromwellEnage  
> Created at: 2019-02-18 20:48:09 UTC  
> Url: https://github.com/boostorg/parameter/issues/72#issuecomment-464872680  

I have release notes for this library set up in HTML format.  How shall I submit them?

---

## Comment 3

> Username: CromwellEnage  
> Created at: 2019-03-07 20:20:33 UTC  
> Url: https://github.com/boostorg/parameter/issues/72#issuecomment-470678870  

With Boost 1.70 closed for major changes today, it looks like we'll have to wait until after release, then target Boost 1.71 instead.

---

## Comment 4

> Username: pdimov  
> Created at: 2019-04-27 01:31:48 UTC  
> Url: https://github.com/boostorg/parameter/issues/72#issuecomment-487243887  

FWIW, I noticed that @jzmaddock intends to merge Graph to master based on https://github.com/boostorg/graph/pull/164, which by the look of the CI results seems to be blocked on Parameter being merged first.

---

## Comment 5

> Username: jzmaddock  
> Created at: 2019-04-27 07:20:23 UTC  
> Url: https://github.com/boostorg/parameter/issues/72#issuecomment-487262955  

Yep, can't merge anything from Graph really till this is merged.

---

## Comment 6

> Username: CromwellEnage  
> Created at: 2019-04-27 12:11:53 UTC  
> Url: https://github.com/boostorg/parameter/issues/72#issuecomment-487281009  

Good sirs,  
  
I've created PR #77 on boostorg/parameter for your review.  If you approve,  
I will merge it.  
  
Very respectfully,  
Cromwell D. Enage

---

## Comment 7

> Username: CromwellEnage  
> Created at: 2019-04-28 04:24:44 UTC  
> Url: https://github.com/boostorg/parameter/issues/72#issuecomment-487342258  

Okay, I've merged the PR since all checks passed, so Graph should be good to go.

---

## Comment 8

> Username: jzmaddock  
> Created at: 2019-04-28 08:36:33 UTC  
> Url: https://github.com/boostorg/parameter/issues/72#issuecomment-487358108  

Many thanks for that - it's much appreciated!

---

## Comment 9

> Username: CromwellEnage  
> Created at: 2019-04-28 21:18:18 UTC  
> Updated at: 2019-04-30 12:35:09 UTC  
> Url: https://github.com/boostorg/parameter/issues/72#issuecomment-487416280  

I've created pull requests boostorg/accumulators#30, boostorg/lockfree#50, and boostorg/signals2#35 to help complete the syncing process for the master branch.

---

## Comment 10

> Username: jzmaddock  
> Created at: 2019-10-08 09:49:19 UTC  
> Url: https://github.com/boostorg/parameter/issues/72#issuecomment-539438995  

Ping?????  
  
Can we merge to master yet?

---

## Comment 11

> Username: CromwellEnage  
> Created at: 2019-10-09 09:43:43 UTC  
> Url: https://github.com/boostorg/parameter/issues/72#issuecomment-539925195  

Sorry, merging the new documentation now.

---

## Comment 12

> Username: Lastique  
> Created at: 2021-12-08 15:47:15 UTC  
> Url: https://github.com/boostorg/parameter/issues/72#issuecomment-988931935  

Develop was merged a while ago.
