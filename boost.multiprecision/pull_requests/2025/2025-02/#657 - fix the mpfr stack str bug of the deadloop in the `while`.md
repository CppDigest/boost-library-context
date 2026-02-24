# #657 fix the mpfr stack str bug of the deadloop in the `while` [Merged]

> Username: FAKERINHEART  
> Created at: 2025-02-24 09:08:25 UTC  
> Updated at: 2025-03-01 03:01:58 UTC  
> Merged at: 2025-02-28 19:21:49 UTC  
> Closed at: 2025-02-28 19:21:50 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/657  



---

## Comment 1

> Username: ckormanyos  
> Created_at: 2025-02-24 12:07:27 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/657#issuecomment-2678227471  

Hi @FAKERINHEART thanks for looking into this. Our workflow requires approvel for first-time contributors. I've just approved your CI workflow run so let's see how the CI tests run through.  
  
Cc: @jzmaddock

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2025-02-24 12:09:48 UTC  
> Updated_at: 2025-02-28 19:22:06 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/657#issuecomment-2678233146  

## [Codecov](https://app.codecov.io/gh/boostorg/multiprecision/pull/657?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 94.1%. Comparing base [(`83d5844`)](https://app.codecov.io/gh/boostorg/multiprecision/commit/83d5844a4280e1d8c6e68f25bbf0da998bd7f297?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`716eb0e`)](https://app.codecov.io/gh/boostorg/multiprecision/commit/716eb0e865f28e568cd3e68c22fd1ce86235c528?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 2 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/multiprecision/pull/657/graphs/tree.svg?width=650&height=150&src=pr&token=SDDBym7Pc9&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/multiprecision/pull/657?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff           @@  
##           develop    #657   +/-   ##  
=======================================  
  Coverage     94.1%   94.1%             
=======================================  
  Files          279     279             
  Lines        28979   28979             
=======================================  
  Hits         27253   27253             
  Misses        1726    1726             
```  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/multiprecision/pull/657?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/multiprecision/pull/657?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [83d5844...716eb0e](https://app.codecov.io/gh/boostorg/multiprecision/pull/657?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 3

> Username: ckormanyos  
> Created_at: 2025-02-24 12:09:54 UTC  
> Updated_at: 2025-02-24 12:10:13 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/657#issuecomment-2678233416  

Oh Also, @FAKERINHEART, do you have a test case that exercises this change to the `for`-loop?. That would be nice because then we could add the test case as well for the new lines.

---

## Comment 4

> Username: jzmaddock  
> Created_at: 2025-02-24 13:35:33 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/657#issuecomment-2678436927  

Actually... is there any reason not to just delete the whole outer loop?  If we enter the loop then the condition that leads to the break will always be taken?

---

## Comment 5

> Username: ckormanyos  
> Created_at: 2025-02-24 13:59:54 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/657#issuecomment-2678498018  

> Actually... is there any reason not to just delete the whole outer loop? If we enter the loop then the condition that leads to the break will always be taken?  
  
Do you mean to remove the outer `while`-loop at line $848$?  
  
I agree @jzmaddock.  
  
- But then also remove the `break;` statement at line $853$ that, at the moment, breaks from the outer `while`-loop, right?  
- Do we need a range-check on `offset + 1`?  
  
Cc: @FAKERINHEART

---

## Comment 6

> Username: FAKERINHEART  
> Created_at: 2025-02-24 14:00:47 UTC  
> Updated_at: 2025-02-24 14:08:15 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/657#issuecomment-2678500246  

> Oh Also, @FAKERINHEART, do you have a test case that exercises this change to the `for`-loop?. That would be nice because then we could add the test case as well for the new lines.  
  
No, I think that it may be no needs to add a new test case. Please look into mpfr.hpp:431, and its commit log: 485fa7c9ae5c568475b376bb5bbf6d123003901e. It was fixed for the `mpfr_float_imp<digits10, allocate_dynamic>` three years ago, but it forgot to fix `mpfr_float_imp<digits10, allocate_stack>` which is for this merge case. They are in the same condition, and the previous has been add tests in the 485fa7c9ae5c568475b376bb5bbf6d123003901e.

---

## Comment 7

> Username: FAKERINHEART  
> Created_at: 2025-02-24 14:03:19 UTC  
> Updated_at: 2025-02-24 14:11:14 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/657#issuecomment-2678507529  

> > Actually... is there any reason not to just delete the whole outer loop? If we enter the loop then the condition that leads to the break will always be taken?  
>   
> Do you mean to remove the outer `while`-loop at line 848 ?  
>   
> I agree @jzmaddock.  
>   
> * But then also remove the `break;` statement at line   
>     853  
>    that, at the moment, breaks from the outer `while`-loop, right?  
> * Do we need a range-check on `offset + 1`?  
>   
> Cc: @FAKERINHEART  
  
If we wanna remove the whole loop, please take its effect both to `mpfr_float_imp<digits10, allocate_dynamic>` and `mpfr_float_imp<digits10, allocate_stack>`.

---

## Comment 8

> Username: FAKERINHEART  
> Created_at: 2025-02-28 02:36:27 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/657#issuecomment-2689576305  

CI has been passed.  
Could we merge this PR please?  
@ckormanyos

---

## Comment 9

> Username: jzmaddock  
> Created_at: 2025-02-28 19:17:57 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/657#issuecomment-2691380305  

Bare with me, I'm not convinced this is the correct fix (or indeed that there is an issue, as the whole loop appears to be a mistake that can never actually loop anyway), I need to investigate some more....

---

## Comment 10

> Username: jzmaddock  
> Created_at: 2025-02-28 19:21:44 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/657#issuecomment-2691386254  

My apologies: you're quite correct, I was completely misreading the code until I saw it with highlighting in my editor!  My bad, will merge...

---

## Comment 11

> Username: mborland  
> Created_at: 2025-02-28 19:30:01 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/657#issuecomment-2691399865  

I'll merge this into master for 1.88 since we're still safe to do so

---

## Comment 12

> Username: FAKERINHEART  
> Created_at: 2025-03-01 03:01:57 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/657#issuecomment-2691881660  

thx~

---
