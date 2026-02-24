# #564 Add Codecov to GitHub Actions [Merged]

> Username: avinal  
> Created at: 2021-02-23 15:47:32 UTC  
> Updated at: 2021-02-28 09:04:40 UTC  
> Merged at: 2021-02-28 08:54:56 UTC  
> Closed at: 2021-02-28 08:54:57 UTC  
> Url: https://github.com/boostorg/gil/pull/564  

<!-- Pull Requests MUST come from topic branch based on develop, and NEVER on `master) --->  
  
### Description  
  
Adds Codecov GitHub Actions   
<!-- What does this pull request do? -->  
  
### References  
  
#548 & #532   
<!-- Any links related to this PR: issues, other PRs, mailing list threads, StackOverflow questions, etc. -->  
  
### Tasklist  
  
<!-- Add YOUR OWN TASK(s), especially if your PR is a work in progress -->  
  
- [x] Add test case(s)  
- [x] Ensure all CI builds pass  
- [ ] Review and approve

---

## Comment 1

> Username: avinal  
> Created_at: 2021-02-23 16:30:35 UTC  
> Url: https://github.com/boostorg/gil/pull/564#issuecomment-784330095  

Please don't worry about commits, I will squash them in a nice single commit :)

---

## Comment 2

> Username: mloskot  
> Created_at: 2021-02-23 17:46:06 UTC  
> Updated_at: 2021-02-23 17:46:54 UTC  
> Url: https://github.com/boostorg/gil/pull/564#issuecomment-784382213  

For the records, the Codedov port to GitHub Actions has already been prototyped by @meshtag  in https://github.com/boostorg/gil/pull/554

---

## Comment 3

> Username: avinal  
> Created_at: 2021-02-24 15:46:32 UTC  
> Url: https://github.com/boostorg/gil/pull/564#issuecomment-785169750  

Hello @mloskot, I need some help now. I am not much experienced with codecov. But as much I know public repositories don't need tokens to upload reports.    
  
The GitHub Actions is perfectly working. You can see the last run here: https://github.com/boostorg/gil/pull/564/checks?check_run_id=1971222975#step:6:1049  
  
It is also being picked by Codecov, which is good  
![image](https://user-images.githubusercontent.com/74113200/109025389-b23e9900-76e4-11eb-8408-a39d43da3515.png)  
  
But for some reason, reports are not showing up. I don't know what else is missing.

---

## Comment 4

> Username: mloskot  
> Created_at: 2021-02-24 16:28:48 UTC  
> Updated_at: 2021-02-24 16:29:22 UTC  
> Url: https://github.com/boostorg/gil/pull/564#issuecomment-785200057  

@avinal I'm afraid I have nothing helpful to offer. I had been struggling in the past with Codecov, https://github.com/codecov/codecov-bash/search?q=mloskot&type=issues, without having a slightest clue what was going wrong.

---

## Comment 5

> Username: avinal  
> Created_at: 2021-02-24 16:29:47 UTC  
> Url: https://github.com/boostorg/gil/pull/564#issuecomment-785200762  

>   
>   
> For the records, the Codedov port to GitHub Actions has already been prototyped by @meshtag in #554  
  
meshtag missed a really important step that is to compile the library with a coverage flag. Then only you can run the codecov routine. (P.S, I missed it too initially 😅)

---

## Comment 6

> Username: avinal  
> Created_at: 2021-02-24 16:40:30 UTC  
> Url: https://github.com/boostorg/gil/pull/564#issuecomment-785208328  

>   
> @avinal I'm afraid I have nothing helpful to offer. I had been struggling in the past with Codecov, https://github.com/codecov/codecov-bash/search?q=mloskot&type=issues, without having a slightest clue what was going wrong.  
  
Ohh, I will try to solve this. 👍

---

## Comment 7

> Username: avinal  
> Created_at: 2021-02-28 06:41:44 UTC  
> Updated_at: 2021-02-28 06:46:49 UTC  
> Url: https://github.com/boostorg/gil/pull/564#issuecomment-787405096  

Hello @mloskot,  
It is all working now.   
![image](https://user-images.githubusercontent.com/74113200/109409682-c4595980-79ba-11eb-8218-b325c6f3a698.png)  
  
There are more things we may add:  
- A `codecov.yml` to further customize reports (I did not notice any significant difference though)  
- A codecov badge, needs to be added from your side  
  
 P.S - By no means it was a `good-first-issue` 😅

---

## Comment 8

> Username: codecov[bot]  
> Created_at: 2021-02-28 07:39:20 UTC  
> Url: https://github.com/boostorg/gil/pull/564#issuecomment-787411311  

# [Codecov](https://codecov.io/gh/boostorg/gil/pull/564?src=pr&el=h1) Report  
> Merging [#564](https://codecov.io/gh/boostorg/gil/pull/564?src=pr&el=desc) (755d1e9) into [develop](https://codecov.io/gh/boostorg/gil/commit/0c0fe1ae79053d8aedfa95eca39329d135e4ea1c?el=desc) (0c0fe1a) will **increase** coverage by `0.78%`.  
> The diff coverage is `97.93%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/gil/pull/564/graphs/tree.svg?width=650&height=150&src=pr&token=rbI5NTc0vX)](https://codecov.io/gh/boostorg/gil/pull/564?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #564      +/-   ##  
===========================================  
+ Coverage    77.81%   78.59%   +0.78%       
===========================================  
  Files          110      117       +7       
  Lines         4367     4980     +613       
===========================================  
+ Hits          3398     3914     +516       
- Misses         969     1066      +97       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/gil/pull/564?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/gil/channel\_algorithm.hpp](https://codecov.io/gh/boostorg/gil/pull/564/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9naWwvY2hhbm5lbF9hbGdvcml0aG0uaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/gil/algorithm.hpp](https://codecov.io/gh/boostorg/gil/pull/564/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9naWwvYWxnb3JpdGhtLmhwcA==) | `67.42% <25.00%> (-1.81%)` | :arrow_down: |  
| [include/boost/gil/rasterization/line.hpp](https://codecov.io/gh/boostorg/gil/pull/564/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9naWwvcmFzdGVyaXphdGlvbi9saW5lLmhwcA==) | `93.33% <93.33%> (ø)` | |  
| [include/boost/gil/histogram.hpp](https://codecov.io/gh/boostorg/gil/pull/564/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9naWwvaGlzdG9ncmFtLmhwcA==) | `97.88% <97.88%> (ø)` | |  
| [include/boost/gil/image\_processing/diffusion.hpp](https://codecov.io/gh/boostorg/gil/pull/564/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9naWwvaW1hZ2VfcHJvY2Vzc2luZy9kaWZmdXNpb24uaHBw) | `98.30% <98.30%> (ø)` | |  
| [include/boost/gil/image\_processing/morphology.hpp](https://codecov.io/gh/boostorg/gil/pull/564/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9naWwvaW1hZ2VfcHJvY2Vzc2luZy9tb3JwaG9sb2d5LmhwcA==) | `98.50% <98.50%> (ø)` | |  
| [include/boost/gil/color\_base.hpp](https://codecov.io/gh/boostorg/gil/pull/564/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9naWwvY29sb3JfYmFzZS5ocHA=) | `98.49% <100.00%> (+0.01%)` | :arrow_up: |  
| [include/boost/gil/color\_convert.hpp](https://codecov.io/gh/boostorg/gil/pull/564/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9naWwvY29sb3JfY29udmVydC5ocHA=) | `100.00% <100.00%> (ø)` | |  
| [include/boost/gil/extension/histogram/std.hpp](https://codecov.io/gh/boostorg/gil/pull/564/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9naWwvZXh0ZW5zaW9uL2hpc3RvZ3JhbS9zdGQuaHBw) | `100.00% <100.00%> (ø)` | |  
| [include/boost/gil/extension/numeric/affine.hpp](https://codecov.io/gh/boostorg/gil/pull/564/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9naWwvZXh0ZW5zaW9uL251bWVyaWMvYWZmaW5lLmhwcA==) | `100.00% <100.00%> (ø)` | |  
| ... and [75 more](https://codecov.io/gh/boostorg/gil/pull/564/diff?src=pr&el=tree-more) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/gil/pull/564?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/gil/pull/564?src=pr&el=footer). Last update [a8cb364...755d1e9](https://codecov.io/gh/boostorg/gil/pull/564?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 9

> Username: mloskot  
> Created_at: 2021-02-28 08:17:14 UTC  
> Url: https://github.com/boostorg/gil/pull/564#issuecomment-787415022  

@avinal Awesome!  
  
> A `codecov.yml` to further customize reports (I did not notice any significant difference though)  
  
AFAIR, my experience with this file was similar.  
  
I sometimes used it to [disable Codecov comment](https://docs.codecov.io/docs/pull-request-comments#disable-comment)   
  
I think we could experiment with the [comment configuration](https://docs.codecov.io/docs/pull-request-comments#disable-comment) to avoid unnecessary PR comments, e.g.  
  
```  
comment:  
  layout: "diff"  
  behavior: default  
  require_changes: true # if true: only post the comment if coverage changes  
```  
  
  
> A codecov badge, needs to be added from your side  
  
We could add two badges to the statuses in the README:  
- https://codecov.io/gh/boostorg/gil/branch/develop/graphs/badge.svg  
- https://codecov.io/gh/boostorg/gil/branch/master/graphs/badge.svg  
  
> P.S - By no means it was a good-first-issue   
  
Well, the difficulty was judge from the GIL point :-)  
  
Kudos for your persistence!

---

## Review 10 [Approved]

> Username: mloskot  
> Created_at: 2021-02-28 08:52:03 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/564#pullrequestreview-600235423  

This looks very good. Thanks!  
  
I'd like to merge it as it is.  
If you'd like to apply any further tweaks, then I suggest to do it in new PRs

---

## Comment 11

> Username: avinal  
> Created_at: 2021-02-28 08:54:01 UTC  
> Updated_at: 2021-02-28 08:54:43 UTC  
> Url: https://github.com/boostorg/gil/pull/564#issuecomment-787418851  

>   
>   
> This looks very good. Thanks!  
>   
> I'd like to merge it as it is.  
> If you'd like to apply any further tweaks, then I suggest to do it in new PRs  
  
Ohh actually I was adding those two changes. I will open a new PR, no worries 😊. Thanks a lot

---

## Comment 12

> Username: mloskot  
> Created_at: 2021-02-28 09:03:08 UTC  
> Url: https://github.com/boostorg/gil/pull/564#issuecomment-787419840  

Sorry for not synchronising our actions better. Please, do new PRs, thanks

---

## Comment 13

> Username: mloskot  
> Created_at: 2021-02-28 09:04:40 UTC  
> Url: https://github.com/boostorg/gil/pull/564#issuecomment-787420040  

I incorrectly put `Closes #565` in the commit message. It should read #548

---
