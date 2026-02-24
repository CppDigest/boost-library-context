# #1307 Update mingw failures to end at GCC-15 [Closed]

> Username: mborland  
> Created at: 2025-08-22 17:49:37 UTC  
> Updated at: 2025-10-26 18:24:42 UTC  
> Closed at: 2025-10-26 11:51:48 UTC  
> Url: https://github.com/boostorg/math/pull/1307  

Got a report on the mailing list that a user hit this configuration error. Checked our links again, and it looks like the bug has finally been fixed in 10/24

---

## Comment 1

> Username: ckormanyos  
> Created_at: 2025-08-22 19:12:40 UTC  
> Updated_at: 2025-08-22 19:16:38 UTC  
> Url: https://github.com/boostorg/math/pull/1307#issuecomment-3215353003  

Hi Matt (@mborland)  
  
I think the CI failures are the same ones we just handled in #1306, which has just now been merged to develop  
  
Cc: @NAThompson

---

## Comment 2

> Username: ckormanyos  
> Created_at: 2025-08-22 19:14:59 UTC  
> Url: https://github.com/boostorg/math/pull/1307#issuecomment-3215358470  

I hopefully just merged the relevant changes in and fired up CI. Let's see if it goes green

---

## Comment 3

> Username: ckormanyos  
> Created_at: 2025-08-22 19:16:12 UTC  
> Url: https://github.com/boostorg/math/pull/1307#issuecomment-3215361071  

> it looks like the bug has finally been fixed in 10/24  
  
Now I see what you mean. That fix took a while. Right on, it's getting better.

---

## Comment 4

> Username: mborland  
> Created_at: 2025-08-25 08:32:53 UTC  
> Url: https://github.com/boostorg/math/pull/1307#issuecomment-3219329528  

@jzmaddock Any chance you can locally test this? There's no runners in CI that have a high enough version of GCC yet.

---

## Comment 5

> Username: ckormanyos  
> Created_at: 2025-08-25 08:54:26 UTC  
> Url: https://github.com/boostorg/math/pull/1307#issuecomment-3219400855  

<img width="895" height="486" alt="image" src="https://github.com/user-attachments/assets/b754d38c-329c-48bf-b1b9-fd57dcdd3ae1" />  
  
It looks like pacman for UCRT64 offers GCC 13 at the moment. That's too old right?  
  
I wonder if we could set up a run on Godbolt?

---

## Comment 6

> Username: mborland  
> Created_at: 2025-08-25 09:14:14 UTC  
> Updated_at: 2025-08-25 09:14:31 UTC  
> Url: https://github.com/boostorg/math/pull/1307#issuecomment-3219468257  

> It looks like pacman for UCRT64 offers GCC 13 at the moment. That's too old right?  
>   
  
Yes. We have GCC 14 in CI where this was still an issue. Since the bug was patched in 10/24 it should have shipped with GCC 15 in 4/25  
  
> I wonder if we could set up a run on Godbolt?  
  
Godbolt only has up to GCC 13

---

## Comment 7

> Username: mborland  
> Created_at: 2025-10-26 11:19:25 UTC  
> Url: https://github.com/boostorg/math/pull/1307#issuecomment-3448433821  

Since MinGW has been updated to GCC 15 in the CI I have rebased this on develop. Hopefully it runs green.

---

## Comment 8

> Username: mborland  
> Created_at: 2025-10-26 11:51:43 UTC  
> Url: https://github.com/boostorg/math/pull/1307#issuecomment-3448452077  

Well despite what we thought this still fails John's threading sanity check. Maybe next time.

---

## Comment 9

> Username: codecov[bot]  
> Created_at: 2025-10-26 12:38:53 UTC  
> Updated_at: 2025-10-26 18:24:42 UTC  
> Url: https://github.com/boostorg/math/pull/1307#issuecomment-3448482348  

## [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1307?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
:white_check_mark: All modified and coverable lines are covered by tests.  
:white_check_mark: Project coverage is 95.28%. Comparing base ([`6ee28f2`](https://app.codecov.io/gh/boostorg/math/commit/6ee28f29f6889a543900d562479284790ddc9b08?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)) to head ([`338b3af`](https://app.codecov.io/gh/boostorg/math/commit/338b3af5078580fe2956ba193d5f7de0b08015e7?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)).  
  
<details><summary>Additional details and impacted files</summary>  
  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/math/pull/1307/graphs/tree.svg?width=650&height=150&src=pr&token=26IDb1OAa7&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/math/pull/1307?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #1307      +/-   ##  
===========================================  
+ Coverage    95.24%   95.28%   +0.04%       
===========================================  
  Files          814      814                
  Lines        69309    67364    -1945       
===========================================  
- Hits         66015    64191    -1824       
+ Misses        3294     3173     -121       
```  
[see 495 files with indirect coverage changes](https://app.codecov.io/gh/boostorg/math/pull/1307/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/math/pull/1307?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1307?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [6ee28f2...338b3af](https://app.codecov.io/gh/boostorg/math/pull/1307?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---
