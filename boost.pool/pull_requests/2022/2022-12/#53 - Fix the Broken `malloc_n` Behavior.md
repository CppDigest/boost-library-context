# #53 Fix the Broken `malloc_n` Behavior [Merged]

> Username: leimao  
> Created at: 2022-12-14 03:31:54 UTC  
> Updated at: 2023-01-15 18:47:16 UTC  
> Merged at: 2023-01-15 12:23:45 UTC  
> Closed at: 2023-01-15 12:23:45 UTC  
> Url: https://github.com/boostorg/pool/pull/53  

Fix #52 and added unit tests.

---

## Comment 1

> Username: leimao  
> Created_at: 2022-12-14 03:39:34 UTC  
> Url: https://github.com/boostorg/pool/pull/53#issuecomment-1350346343  

@mclow Could you please approve me running the test workflow to see if it will pass the existing unit tests?

---

## Comment 2

> Username: leimao  
> Created_at: 2022-12-14 06:12:31 UTC  
> Url: https://github.com/boostorg/pool/pull/53#issuecomment-1350455002  

Also tagging @jzmaddock for enabling first-time user workflow.

---

## Comment 3

> Username: leimao  
> Created_at: 2022-12-14 07:17:50 UTC  
> Url: https://github.com/boostorg/pool/pull/53#issuecomment-1350524601  

The patch works. Can someone please review and approve? Thank you.

---

## Comment 4

> Username: jzmaddock  
> Created_at: 2022-12-14 12:53:33 UTC  
> Url: https://github.com/boostorg/pool/pull/53#issuecomment-1351299024  

I've approved the run, but I'm not sure how up to date the CI scripts are.  We've also often talked about marking this library deprecated, but that appears not to have happened yet :(

---

## Comment 5

> Username: codecov[bot]  
> Created_at: 2022-12-14 13:23:19 UTC  
> Updated_at: 2022-12-24 09:55:11 UTC  
> Url: https://github.com/boostorg/pool/pull/53#issuecomment-1351347268  

# [Codecov](https://codecov.io/gh/boostorg/pool/pull/53?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#53](https://codecov.io/gh/boostorg/pool/pull/53?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (6ec9801) into [develop](https://codecov.io/gh/boostorg/pool/commit/600bcb027379b0670ccecf14f380f77e1264037f?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (600bcb0) will **increase** coverage by `0.23%`.  
> The diff coverage is `100.00%`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/pool/pull/53/graphs/tree.svg?width=650&height=150&src=pr&token=blmk8CZxg2&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/pool/pull/53?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop      #53      +/-   ##  
===========================================  
+ Coverage    93.48%   93.71%   +0.23%       
===========================================  
  Files            9        9                
  Lines          583      589       +6       
===========================================  
+ Hits           545      552       +7       
+ Misses          38       37       -1       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/pool/pull/53?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/pool/simple\_segregated\_storage.hpp](https://codecov.io/gh/boostorg/pool/pull/53/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9wb29sL3NpbXBsZV9zZWdyZWdhdGVkX3N0b3JhZ2UuaHBw) | `95.74% <100.00%> (+0.29%)` | :arrow_up: |  
| [include/boost/pool/pool.hpp](https://codecov.io/gh/boostorg/pool/pull/53/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9wb29sL3Bvb2wuaHBw) | `96.61% <0.00%> (+0.33%)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/pool/pull/53?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/pool/pull/53?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [600bcb0...6ec9801](https://codecov.io/gh/boostorg/pool/pull/53?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 6

> Username: leimao  
> Created_at: 2022-12-14 16:20:17 UTC  
> Url: https://github.com/boostorg/pool/pull/53#issuecomment-1351718579  

> I've approved the run, but I'm not sure how up to date the CI scripts are. We've also often talked about marking this library deprecated, but that appears not to have happened yet :(  
  
@jzmaddock Do you still suggest merging this patch? There is a small coverage check failing which I shall further investigate.

---

## Comment 7

> Username: jzmaddock  
> Created_at: 2022-12-14 18:44:46 UTC  
> Url: https://github.com/boostorg/pool/pull/53#issuecomment-1351955312  

>Do you still suggest merging this patch? There is a small coverage check failing which I shall further investigate.  
  
I know it's a relatively trivial patch, but I still need to find the time to do a proper eyeball-checkover before merging.  Please nag me if I don't get to it soon-ish.

---

## Comment 8

> Username: leimao  
> Created_at: 2022-12-14 19:08:30 UTC  
> Url: https://github.com/boostorg/pool/pull/53#issuecomment-1352002687  

Thanks @jzmaddock. Take your time. Some of the new unit tests I added this morning trying to remedy the unit test coverage issues yesterday seems having some problems. I will check and try fixing them later today or tomorrow.

---

## Comment 9

> Username: leimao  
> Created_at: 2022-12-15 03:21:49 UTC  
> Updated_at: 2022-12-15 03:22:33 UTC  
> Url: https://github.com/boostorg/pool/pull/53#issuecomment-1352507134  

@jzmaddock I tested the new patch I just uploaded locally and it worked. Hope this time the test coverage issue can be fixed. Is there anyway that I can measure the test coverage locally BTW?

---

## Comment 10

> Username: leimao  
> Created_at: 2022-12-16 22:53:34 UTC  
> Url: https://github.com/boostorg/pool/pull/53#issuecomment-1355750604  

@jzmaddock Request running more test workflows.

---

## Comment 11

> Username: leimao  
> Created_at: 2022-12-19 03:06:37 UTC  
> Updated_at: 2022-12-19 03:07:01 UTC  
> Url: https://github.com/boostorg/pool/pull/53#issuecomment-1357021515  

@jzmaddock With the new unit test, it added one additional coverage comparing to the previous commit [cf9b5c4](https://github.com/boostorg/pool/pull/53/commits/cf9b5c4a820e4221cbcf50d652b53742777de1e9). But it's still missing one coverage comparing to the develop branch. Is it something that has to be fixed in order to get the pull request merged? Thanks.

---

## Comment 12

> Username: leimao  
> Created_at: 2022-12-19 04:16:44 UTC  
> Url: https://github.com/boostorg/pool/pull/53#issuecomment-1357071543  

@jzmaddock Added one more unit test. I ran some local unit test to see if the logic will flow to the positions where the coverage test failed. It seems that it will help. Please run the test workflow again. Thanks.

---

## Comment 13

> Username: leimao  
> Created_at: 2022-12-24 06:47:38 UTC  
> Url: https://github.com/boostorg/pool/pull/53#issuecomment-1364473920  

@jzmaddock

---

## Comment 14

> Username: leimao  
> Created_at: 2022-12-27 06:05:26 UTC  
> Url: https://github.com/boostorg/pool/pull/53#issuecomment-1365636316  

@jzmaddock It seems that it has passed all the checks. Could you please review and merge if possible?

---

## Comment 15

> Username: leimao  
> Created_at: 2022-12-30 06:09:00 UTC  
> Url: https://github.com/boostorg/pool/pull/53#issuecomment-1367744989  

@jzmaddock Bothering you again :)

---

## Comment 16

> Username: leimao  
> Created_at: 2023-01-02 07:17:14 UTC  
> Url: https://github.com/boostorg/pool/pull/53#issuecomment-1368703732  

@jzmaddock @pabristow Can some of you please review?

---

## Comment 17

> Username: leimao  
> Created_at: 2023-01-09 01:44:19 UTC  
> Url: https://github.com/boostorg/pool/pull/53#issuecomment-1375007987  

@jzmaddock

---

## Comment 18

> Username: leimao  
> Created_at: 2023-01-14 21:57:55 UTC  
> Url: https://github.com/boostorg/pool/pull/53#issuecomment-1382933659  

@jzmaddock @mclow

---

## Comment 19

> Username: jzmaddock  
> Created_at: 2023-01-15 12:23:36 UTC  
> Url: https://github.com/boostorg/pool/pull/53#issuecomment-1383137605  

This all checks out, thanks for persevering!

---

## Comment 20

> Username: leimao  
> Created_at: 2023-01-15 18:47:15 UTC  
> Url: https://github.com/boostorg/pool/pull/53#issuecomment-1383222962  

> This all checks out, thanks for persevering!  
  
Thank you very much!

---
