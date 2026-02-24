# #587 Bugfix/omit selfturn condition [Merged]

> Username: barendgehrels  
> Created at: 2019-05-01 20:11:02 UTC  
> Updated at: 2019-07-18 11:56:53 UTC  
> Merged at: 2019-05-07 16:31:42 UTC  
> Closed at: 2019-05-07 16:31:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/587  

This is a very small PR, changing only one condition (fixing a case without rescaling, all other cases stay fine) and adding a few cases based on precision research/enhancements

---

## Comment 1

> Username: barendgehrels  
> Created_at: 2019-05-01 20:13:23 UTC  
> Url: https://github.com/boostorg/geometry/pull/587#issuecomment-488401699  

@awulkiew - how can I trigger all tests on this PR? Is that possible?  
Locally I tested all set_operations and buffer, which are fine - but it would be good to test all of it.

---

## Comment 2

> Username: awulkiew  
> Created_at: 2019-05-01 22:23:00 UTC  
> Url: https://github.com/boostorg/geometry/pull/587#issuecomment-488460045  

@barendgehrels What do you mean? Do you want to run all of the tests at CircleCI? If that's the case then you'd have to either remove branch filters from CircleCI jobs or add your branch there.  
  
Filters are defined here:  
https://github.com/boostorg/geometry/blob/develop/.circleci/config.yml#L487  
  
then they are applied to separate jobs e.g. like that:  
https://github.com/boostorg/geometry/blob/develop/.circleci/config.yml#L555  
  
minimal test is run for all branches other than master and develop:  
https://github.com/boostorg/geometry/blob/develop/.circleci/config.yml#L631  
  
Have in mind that at CircleCI not all of the tests are executed, e.g. all of the extensions and some index tests are ommited.  
  
Furthermore at CircleCI only one OS and compiler is used. If you want more comprehensive tests it's still better to run locally with several compilers or merge to develop and wait for the regression matrix update.

---

## Comment 3

> Username: barendgehrels  
> Created_at: 2019-05-04 10:55:08 UTC  
> Url: https://github.com/boostorg/geometry/pull/587#issuecomment-489316499  

@awulkiew  - thanks for the info! For now I run all the tests locally

---

## Review 4 [Approved]

> Username: vissarion  
> Created_at: 2019-05-06 13:01:22 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/587#pullrequestreview-233965724  

Thanks I am ok with merging. One quick question about the decimal numbers in test (e.g. https://github.com/boostorg/geometry/pull/587/files#diff-e86cd4a9c6594a3f22e85d3591381729R808) Where do they come from? Are they abstract? Is there some threshold under which the results are wrong? Also, do the new tests pass without the new change https://github.com/boostorg/geometry/pull/587/files#diff-0f40e8d268f98bb2da5e03fc808ee08fR806 ?

---

## Comment 5

> Username: barendgehrels  
> Created_at: 2019-05-06 16:04:32 UTC  
> Url: https://github.com/boostorg/geometry/pull/587#issuecomment-489676154  

@vissarion : good points. I have a new robustness test (still to be committed) for removing rescaling, which scans input (adding very small values to some coordinates) and reports if they are failing. Very useful to find border cases.  
No, in the final (ideal) case they should all pass, and there will be some thresholds but I try to limit that as much as possible.  
Yes, these tests will also pass without the new change, in that sense it is unrelated. But the change is necessary for some other (yet uncommitted) code.  
  
@awulkiew  : all tests pass, I think it can be merged.

---
