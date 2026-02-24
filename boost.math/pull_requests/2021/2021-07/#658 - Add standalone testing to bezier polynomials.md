# #658 Add standalone testing to bezier polynomials [Merged]

> Username: mborland  
> Created at: 2021-07-04 18:48:49 UTC  
> Updated at: 2021-07-05 23:41:07 UTC  
> Merged at: 2021-07-04 20:39:35 UTC  
> Closed at: 2021-07-04 20:39:35 UTC  
> Url: https://github.com/boostorg/math/pull/658  

A copy of #656 rebased against the develop branch.

---

## Comment 1

> Username: NAThompson  
> Created_at: 2021-07-04 20:36:47 UTC  
> Url: https://github.com/boostorg/math/pull/658#issuecomment-873657922  

Yup, you're right; it's gone. That's a bug in github!

---

## Comment 2

> Username: NAThompson  
> Created_at: 2021-07-04 20:40:21 UTC  
> Url: https://github.com/boostorg/math/pull/658#issuecomment-873658447  

I merged before testing completed because 1) we're a bit backed up, 2) we know develop is broken. We can open a new PR if necessary.

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2021-07-05 18:14:02 UTC  
> Url: https://github.com/boostorg/math/pull/658#issuecomment-874267697  

> I merged before testing completed because 1) we're a bit backed up, 2) we know develop is broken. We can open a new PR if necessary.  
  
What's broken?  There's stuff that still needs merging to master...

---

## Comment 4

> Username: mborland  
> Created_at: 2021-07-05 18:23:47 UTC  
> Url: https://github.com/boostorg/math/pull/658#issuecomment-874271538  

> > I merged before testing completed because 1) we're a bit backed up, 2) we know develop is broken. We can open a new PR if necessary.  
>   
> What's broken? There's stuff that still needs merging to master...  
  
The bezier polynomial test was broken on develop but fixed here. CI looks fine now.

---

## Comment 5

> Username: NAThompson  
> Created_at: 2021-07-05 23:41:07 UTC  
> Url: https://github.com/boostorg/math/pull/658#issuecomment-874370106  

The code wasn't so much broken as the process. Matt made a PR against my PR. I merged that PR with the intent on immediately merging Matt's PR on top of mine, for a clean build. But I deleted my branch after merge, which autoclosed Matt's PR. I "undeleted" the branch but Matt's PR was pwned. So Matt had to reopen a new PR, which was then on a broken develop.

---
