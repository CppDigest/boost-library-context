# #64 Added at(size_type) [Merged]

> Username: akr-akari  
> Created at: 2022-04-22 11:58:24 UTC  
> Updated at: 2022-05-03 01:02:40 UTC  
> Merged at: 2022-05-03 00:38:40 UTC  
> Closed at: 2022-05-03 00:38:40 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/64  

I'm so sorry that I want to add this member to facilitate debugging, and this member is required according to the C++ Core Guides. Thank you!

---

## Comment 1

> Username: mclow  
> Created_at: 2022-04-22 14:22:21 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/64#issuecomment-1106567402  

I see no tests.

---

## Comment 2

> Username: akr-akari  
> Created_at: 2022-04-22 16:55:38 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/64#issuecomment-1106694655  

> I see no tests.  
  
Sorry, I have added tests now.

---

## Comment 3

> Username: codecov[bot]  
> Created_at: 2022-04-27 11:34:13 UTC  
> Updated_at: 2022-04-27 21:21:28 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/64#issuecomment-1110894473  

# [Codecov](https://codecov.io/gh/boostorg/dynamic_bitset/pull/64?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#64](https://codecov.io/gh/boostorg/dynamic_bitset/pull/64?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (b3d14a2) into [develop](https://codecov.io/gh/boostorg/dynamic_bitset/commit/eb3f18086ec5e3d5f04bb3d8bd2bca8bc345b251?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (eb3f180) will **increase** coverage by `0.02%`.  
> The diff coverage is `100.00%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/dynamic_bitset/pull/64/graphs/tree.svg?width=650&height=150&src=pr&token=PVG5jth1ez&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/dynamic_bitset/pull/64?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop      #64      +/-   ##  
===========================================  
+ Coverage    95.78%   95.80%   +0.02%       
===========================================  
  Files            5        5                
  Lines          664      668       +4       
===========================================  
+ Hits           636      640       +4       
  Misses          28       28                
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/dynamic_bitset/pull/64?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/dynamic\_bitset/dynamic\_bitset.hpp](https://codecov.io/gh/boostorg/dynamic_bitset/pull/64/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9keW5hbWljX2JpdHNldC9keW5hbWljX2JpdHNldC5ocHA=) | `95.49% <100.00%> (+0.02%)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/dynamic_bitset/pull/64?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/dynamic_bitset/pull/64?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [eb3f180...b3d14a2](https://codecov.io/gh/boostorg/dynamic_bitset/pull/64?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).

---

## Review 4 [Changes requested]

> Username: jeking3  
> Created_at: 2022-04-27 12:00:01 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/dynamic_bitset/pull/64#pullrequestreview-954774270  

Still missing a negative test (see codecov's analysis in "Files changed" tab).

---

## Comment 5

> Username: jeking3  
> Created_at: 2022-04-27 18:22:35 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/64#issuecomment-1111338323  

I suspect there may be a good reason why the reference return comes before the bool return in the definitions of operator [], so I would recommend using the same ordering for the at() method.  It's also possible I'm just being a little paranoid. :)

---

## Comment 6

> Username: akr-akari  
> Created_at: 2022-04-27 18:48:08 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/64#issuecomment-1111361136  

OK, I have changed the order of at().

---

## Review 7 [Commented]

> Username: jeking3  
> Created_at: 2022-04-29 22:16:38 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/dynamic_bitset/pull/64#pullrequestreview-958332166  

📁 test/bitset_test.hpp

```diff
1240 |+               BOOST_TEST(!!ex.what());
1241 |+           }
1242 |+           catch (...)
```

> Username: jeking3  
> Created_at: 2022-04-29 22:16:38 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/64#discussion_r862210006  

No need to change this, but I find just letting the exception fail the test makes it easier to debug.  If something throws that is not caught it will crash the test which is fine.  So these catch-all clauses in tests are usually unnecessary.

> Username: akr-akari  
> Created_at: 2022-04-29 23:44:41 UTC  
> Updated_at: 2022-04-30 00:20:39 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/64#discussion_r862259520  

Thank you for your review, I agree with you. Here I refer to the same approach for the `to_ulong()` test in line 763.


---

## Comment 8

> Username: jeking3  
> Created_at: 2022-04-29 22:18:23 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/64#issuecomment-1113809849  

@mclow this looks good to me, would you like to review it again?

---
