# #707 Change CircleCI runner image to a "known good" [Merged]

> Username: jzmaddock  
> Created at: 2025-06-30 15:16:45 UTC  
> Updated at: 2025-07-01 18:12:57 UTC  
> Merged at: 2025-07-01 12:14:42 UTC  
> Closed at: 2025-07-01 12:14:42 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/707  



---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2025-06-30 17:02:03 UTC  
> Updated_at: 2025-07-01 12:14:49 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/707#issuecomment-3020026403  

## [Codecov](https://app.codecov.io/gh/boostorg/multiprecision/pull/707?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 94.6%. Comparing base [(`1301aa2`)](https://app.codecov.io/gh/boostorg/multiprecision/commit/1301aa203fad6faf676072c33c0a6e97b4cc31f3?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`7494c2d`)](https://app.codecov.io/gh/boostorg/multiprecision/commit/7494c2d17aea0ec8dca95b9df976d9a37b74d05e?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 5 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/multiprecision/pull/707/graphs/tree.svg?width=650&height=150&src=pr&token=SDDBym7Pc9&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/multiprecision/pull/707?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff           @@  
##           develop    #707   +/-   ##  
=======================================  
  Coverage     94.6%   94.6%             
=======================================  
  Files          295     295             
  Lines        30728   30728             
=======================================  
  Hits         29042   29042             
  Misses        1686    1686             
```  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/multiprecision/pull/707?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/multiprecision/pull/707?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [1301aa2...7494c2d](https://app.codecov.io/gh/boostorg/multiprecision/pull/707?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---

## Comment 2

> Username: ckormanyos  
> Created_at: 2025-06-30 17:29:01 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/707#issuecomment-3020111788  

This is awesome @jzmaddock it is really good to see GREEN all across the board! Yeah!

---

## Comment 3

> Username: ckormanyos  
> Created_at: 2025-06-30 17:31:02 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/707#issuecomment-3020117416  

It's a mildly bigger discussion, but I think we could potentially start considering branch protection for Multiprecision. Maybe a bit down the road if this all stays stable.  
  
Cc: @mborland

---

## Comment 4

> Username: mborland  
> Created_at: 2025-06-30 17:40:08 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/707#issuecomment-3020144092  

Here it's not so much branch protection as it is protection from external variables. All the modular changes are merged into develop, but not yet into master. I expect to see some red that's not real red when we merge to master for next release.

---

## Comment 5

> Username: ckormanyos  
> Created_at: 2025-06-30 18:00:15 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/707#issuecomment-3020203100  

Hi Matt, I meant more along the lines of the following, ...  
  
- GHA has become quite fast and relatively reliable. This is based on a lot of work and quite honestly the depth of runner availability now through the C++ Alliance.  
- Drone is running quickly and somewhat reliably.  
- And CircleCI  
  
So what I would like is the following. When I develop, I often times don't look at every single CI run. And I don't want to. I would like the meaning of green to actually mean GREEN.  
  
So when I get green, I merge to develop. We are close to this quality level.  
  
You won't like this, but I also would like GHA to run on push-to-branch _BEFORE_ I go for the PR. I'm often hitting real rookie errors in the first PR run. And I should have swept those out on a quick GHA run prior to really raising the PR.  
  
Cc: @jzmaddock

---

## Comment 6

> Username: mborland  
> Created_at: 2025-06-30 18:06:38 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/707#issuecomment-3020221196  

Couldn't rookie errors be determined by running the tests locally before push? I don't think we should be DDoSing shared resources like that. I run a few docker containers with various architectures that plug directly in my IDE (Clion). It's a trivial dropdown to change what I'm running on.  
  
<img width="1313" alt="image" src="https://github.com/user-attachments/assets/81c0a5be-cd90-4cb1-8992-c740535745cc" />

---

## Comment 7

> Username: ckormanyos  
> Created_at: 2025-06-30 18:11:38 UTC  
> Updated_at: 2025-06-30 18:13:13 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/707#issuecomment-3020233780  

> Couldn't rookie errors be determined by running the tests locally before push?  
  
Yes. Whithout hijacking this PR too much (my mistake in the first place).  
  
> run a few docker containers  
  
To be direct, I never really figured out how to do that. And just (lazily) mis-used the runners. If you could walk me through it, ... then problem solved.  
  
This is my weakness, not the system's. So fundamentally you're right. And it would _modrnize_ me into the container stuff, which is somewhat unknown to me.

---

## Comment 8

> Username: mborland  
> Created_at: 2025-06-30 18:17:26 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/707#issuecomment-3020249216  

>   
> To be direct, I never really figured out how to do that. And just (lazily) mis-used the runners. If you could walk me through it, ... then problem solved.  
>   
  
Bring your laptop and some Dinkelacker Privat with you when you come visit, then I'm sure we can get it working.

---

## Comment 9

> Username: ckormanyos  
> Created_at: 2025-06-30 18:26:38 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/707#issuecomment-3020275054  

> Bring your laptop and some Dinkelacker Privat with you when you come visit, then I'm sure we can get it working.  
  
Alright!!!

---

## Comment 10

> Username: ckormanyos  
> Created_at: 2025-07-01 18:12:57 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/707#issuecomment-3025052437  

Sadly, I'm still not finished with this.  
  
I would really like the meaning of green to mean _green_. I am talking about branch protection here, not running tests locally or remote.  
  
We should not be allowed to push unless it all goes green.  
  
This might seem radical. I do not know if all of the runners have this reliability. So just take this as a _wish_  so far. But I sure would like branch protection if/when it becomes reliable.  
  
Cc: @jzmaddock and @mborland

---
