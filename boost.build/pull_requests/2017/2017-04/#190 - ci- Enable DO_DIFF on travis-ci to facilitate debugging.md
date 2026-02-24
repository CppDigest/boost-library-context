# #190 ci: Enable DO_DIFF on travis-ci to facilitate debugging. [Merged]

> Username: tee3  
> Created at: 2017-04-02 19:29:09 UTC  
> Updated at: 2021-10-02 22:18:40 UTC  
> Merged at: 2017-04-03 04:31:01 UTC  
> Closed at: 2017-04-03 04:31:01 UTC  
> Url: https://github.com/boostorg/build/pull/190  

The Boost.Build tests provide a mechanism to observe the difference between the expected output of a test and the output of a failing test.  This is useful in a CI context because it is time-consuming to resolve issues that occur only on the cloud-based CI system.  
  
I don't believe that this will cause issues due to large prints as most tests succeed and print nothing.  It is only when a test fails does this print anything and this should be rare for CI.  Furthermore, I believe that most tests do not typically print out large chunks of text when running.  
  
This has passed the testing on travis-ci on a local branch.

---

## Comment 1

> Username: grafikrobot  
> Created_at: 2017-04-03 02:38:31 UTC  
> Url: https://github.com/boostorg/build/pull/190#issuecomment-291036936  

Could you add the equivalent on Appveyor for consistency?

---

## Comment 2

> Username: tee3  
> Created_at: 2017-04-03 02:48:51 UTC  
> Url: https://github.com/boostorg/build/pull/190#issuecomment-291037857  

Sure thing.

---

## Comment 3

> Username: tee3  
> Created_at: 2017-04-03 03:13:08 UTC  
> Url: https://github.com/boostorg/build/pull/190#issuecomment-291040039  

I've added it to appveyor.  I tested it separately by echoing the value of %DO_DIFF% and it appears to work properly.  
  
Note that the appveyor builds are all failing for other reasons, I believe to the ongoing development of the VC2017 toolset.

---
