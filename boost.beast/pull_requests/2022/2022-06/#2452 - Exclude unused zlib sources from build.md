# #2452 Exclude unused zlib sources from build [Closed]

> Username: sehe  
> Created at: 2022-06-15 17:57:34 UTC  
> Updated at: 2022-06-22 20:02:56 UTC  
> Closed at: 2022-06-22 20:02:55 UTC  
> Url: https://github.com/boostorg/beast/pull/2452  

Recent update zlib-1.2.11->1.2.12 included included a number of source  
files in the Bjam/CMake changes that were previously excluded.  
  
This leads to a bunch of C99 violation warnings (implicit function  
declarations).  
  
This commit excludes the culprits again, but leaves them actively  
commented-out so the same mistake is less likely to happen by accident  
in the future.

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2022-06-15 18:22:13 UTC  
> Url: https://github.com/boostorg/beast/pull/2452#issuecomment-1156789780  

![pullrequest](https://2452.beast.tracing.cppalliance.org/pullrequest-5201cc91.png)  
Timeline tracing charts: [https://2452.beast.tracing.cppalliance.org/index.html](https://2452.beast.tracing.cppalliance.org/index.html)

---

## Comment 2

> Username: alandefreitas  
> Created_at: 2022-06-15 18:22:36 UTC  
> Updated_at: 2022-06-15 18:22:52 UTC  
> Url: https://github.com/boostorg/beast/pull/2452#issuecomment-1156790111  

Nice. Commenting out the files is a great idea. It would be very hard to figure that out in https://github.com/boostorg/beast/pull/2435 without these comments or anything to indicate these files should be ignored.

---

## Comment 3

> Username: codecov[bot]  
> Created_at: 2022-06-15 18:53:45 UTC  
> Updated_at: 2022-06-15 18:54:44 UTC  
> Url: https://github.com/boostorg/beast/pull/2452#issuecomment-1156815875  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/2452?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#2452](https://codecov.io/gh/boostorg/beast/pull/2452?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (f110cb9) into [develop](https://codecov.io/gh/boostorg/beast/commit/c2cff2254a903beb93ea5b434b000dca28f870c5?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (c2cff22) will **decrease** coverage by `0.03%`.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/2452/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/beast/pull/2452?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #2452      +/-   ##  
===========================================  
- Coverage    94.75%   94.71%   -0.04%       
===========================================  
  Files          152      152                
  Lines        11868    11868                
===========================================  
- Hits         11245    11241       -4       
- Misses         623      627       +4       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/2452?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/websocket/impl/read.hpp](https://codecov.io/gh/boostorg/beast/pull/2452/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9yZWFkLmhwcA==) | `96.50% <0.00%> (-0.67%)` | :arrow_down: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/2452?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/2452?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [c2cff22...f110cb9](https://codecov.io/gh/boostorg/beast/pull/2452?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).

---

## Comment 4

> Username: sehe  
> Created_at: 2022-06-15 19:11:18 UTC  
> Url: https://github.com/boostorg/beast/pull/2452#issuecomment-1156829921  

@sdarwin Do you understand why code-coverage keeps complaining that coverage is reduced on branches that clearly don't impact that file? Is it to do with changed build matrix configs perchance? Can we "reset" it or what can we do to stop the noise?

---

## Comment 5

> Username: vinniefalco  
> Created_at: 2022-06-18 15:23:18 UTC  
> Url: https://github.com/boostorg/beast/pull/2452#issuecomment-1159483041  

I would have just left the commit message empty except for the first line and the "close 2452." Being lazy is often more correcterer.

---

## Comment 6

> Username: sdarwin  
> Created_at: 2022-06-18 16:11:13 UTC  
> Url: https://github.com/boostorg/beast/pull/2452#issuecomment-1159490368  

> @sdarwin Do you understand why code-coverage keeps complaining that coverage is reduced on branches that clearly don't impact that file? Is it to do with changed build matrix configs perchance? Can we "reset" it or what can we do to stop the noise?  
  
@sehe from https://docs.codecov.com/docs/commit-status this looks like a plausible fix to the problem, add the following section to .codecov.yml at the end of the file.  
  
```  
coverage:  
  status:  
    project:  
      default:  
        threshold: 1%  
```

---

## Comment 7

> Username: vinniefalco  
> Created_at: 2022-06-18 17:25:56 UTC  
> Url: https://github.com/boostorg/beast/pull/2452#issuecomment-1159519567  

Umm... that just covers up the problem :) WHY is it report a discrepancy?

---

## Comment 8

> Username: sdarwin  
> Created_at: 2022-06-18 17:34:29 UTC  
> Url: https://github.com/boostorg/beast/pull/2452#issuecomment-1159520992  

The coverage difference is extremely small such as .03%. This change could  
be caused by adding or removing any  lines of code, affecting the total  
count,  which is then used in the percentage calculation.  
  
On Sat, Jun 18, 2022, 11:26 AM Vinnie Falco ***@***.***>  
wrote:  
  
> Umm... that just covers up the problem :) WHY is it report a discrepancy?  
>  
> —  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/beast/pull/2452#issuecomment-1159519567>, or  
> unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/AAJTCS6XA3WRGRDPCFWMSNTVPYBC7ANCNFSM5Y4FCQOA>  
> .  
> You are receiving this because you were mentioned.Message ID:  
> ***@***.***>  
>

---

## Comment 9

> Username: vinniefalco  
> Created_at: 2022-06-18 17:42:44 UTC  
> Url: https://github.com/boostorg/beast/pull/2452#issuecomment-1159522380  

> This change could be caused by adding or removing any  lines of code  
  
Yes of course, but it is often the case that a pull request does not change any lines of covered code, such as in this pull request. And yet the coverage changes.

---

## Comment 10

> Username: sdarwin  
> Created_at: 2022-06-18 18:19:51 UTC  
> Url: https://github.com/boostorg/beast/pull/2452#issuecomment-1159531631  

In a percentage calculation there is a numerator and a denominator.   If  
"any" character changes whatsoever, the total sum of all code changes.  
Regardless of "covered code".   Then, the covered is compared against the  
total sum.      (unless this isn't right.   :-). Just the theory.  
  
On Sat, Jun 18, 2022, 11:42 AM Vinnie Falco ***@***.***>  
wrote:  
  
> This change could be caused by adding or removing any lines of code  
>  
> Yes of course, but it is often the case that a pull request does not  
> change any lines of covered code, such as in this pull request. And yet the  
> coverage changes.  
>  
> —  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/beast/pull/2452#issuecomment-1159522380>, or  
> unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/AAJTCSYYYHPDXYIXWVMQK4LVPYDB7ANCNFSM5Y4FCQOA>  
> .  
> You are receiving this because you were mentioned.Message ID:  
> ***@***.***>  
>

---

## Comment 11

> Username: sdarwin  
> Created_at: 2022-06-18 18:27:14 UTC  
> Url: https://github.com/boostorg/beast/pull/2452#issuecomment-1159532912  

Or, .03% is an artifact of some internal calculation that codecov is making  
and it is an error.  
  
On Sat, Jun 18, 2022, 12:19 PM Sam Darwin ***@***.***> wrote:  
  
> In a percentage calculation there is a numerator and a denominator.   If  
> "any" character changes whatsoever, the total sum of all code changes.  
> Regardless of "covered code".   Then, the covered is compared against the  
> total sum.      (unless this isn't right.   :-). Just the theory.  
>  
> On Sat, Jun 18, 2022, 11:42 AM Vinnie Falco ***@***.***>  
> wrote:  
>  
>> This change could be caused by adding or removing any lines of code  
>>  
>> Yes of course, but it is often the case that a pull request does not  
>> change any lines of covered code, such as in this pull request. And yet the  
>> coverage changes.  
>>  
>> —  
>> Reply to this email directly, view it on GitHub  
>> <https://github.com/boostorg/beast/pull/2452#issuecomment-1159522380>,  
>> or unsubscribe  
>> <https://github.com/notifications/unsubscribe-auth/AAJTCSYYYHPDXYIXWVMQK4LVPYDB7ANCNFSM5Y4FCQOA>  
>> .  
>> You are receiving this because you were mentioned.Message ID:  
>> ***@***.***>  
>>  
>

---

## Comment 12

> Username: alandefreitas  
> Created_at: 2022-06-18 21:29:08 UTC  
> Url: https://github.com/boostorg/beast/pull/2452#issuecomment-1159567938  

This fixes #2462, right? We should link that.

---

## Comment 13

> Username: vinniefalco  
> Created_at: 2022-06-21 23:33:26 UTC  
> Url: https://github.com/boostorg/beast/pull/2452#issuecomment-1162461439  

good

---

## Comment 14

> Username: alandefreitas  
> Created_at: 2022-06-22 20:02:55 UTC  
> Url: https://github.com/boostorg/beast/pull/2452#issuecomment-1163547625  

Merged at 4f3276fd704009c0417fa1bca282f7f82acd3d1e

---
