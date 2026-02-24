# #20 Add support for modular build structure. [Merged]

> Username: grafikrobot  
> Created at: 2024-07-20 22:31:45 UTC  
> Updated at: 2025-06-26 14:32:16 UTC  
> Merged at: 2025-06-26 14:01:59 UTC  
> Closed at: 2025-06-26 14:01:59 UTC  
> Url: https://github.com/boostorg/align/pull/20  

This is part of the effort to make the Boost libraries "modular" for build and consumption. See https://lists.boost.org/Archives/boost/2024/01/255704.php and https://github.com/grafikrobot/boost-b2-modular/blob/b2-modular/README.adoc for more information.  
  
This PR depends on the following other PRs being merged to both develop and master branches of the respective repos:  
  
- https://github.com/boostorg/boost/pull/854  
  
This PR will be changed to ready for review, i.e. not draft, when the above are merged. Do not merge this one until that time.

---

## Comment 1

> Username: grafikrobot  
> Created_at: 2024-08-18 16:03:02 UTC  
> Url: https://github.com/boostorg/align/pull/20#issuecomment-2295311079  

Please review and merge this PR at your earliest convenience.

---

## Comment 2

> Username: pdimov  
> Created_at: 2025-05-02 16:04:48 UTC  
> Url: https://github.com/boostorg/align/pull/20#issuecomment-2847588684  

Too much stuff commented out in CI; while I understand why it's done, it's not for me to merge such changes.

---

## Comment 3

> Username: grafikrobot  
> Created_at: 2025-05-02 16:22:33 UTC  
> Url: https://github.com/boostorg/align/pull/20#issuecomment-2847622796  

Okay then.. @glenfe please merge this PR.

---

## Comment 4

> Username: pdimov  
> Created_at: 2025-06-26 12:33:29 UTC  
> Updated_at: 2025-06-26 12:33:44 UTC  
> Url: https://github.com/boostorg/align/pull/20#issuecomment-3008333420  

Way too many unnecessary CI changes... what's the point of renaming `cxxstd` to `standard`, for instance? This just generates diffs that have nothing to do with the point of the PR.

---

## Comment 5

> Username: grafikrobot  
> Created_at: 2025-06-26 13:50:17 UTC  
> Url: https://github.com/boostorg/align/pull/20#issuecomment-3008574874  

> Way too many unnecessary CI changes... what's the point of renaming `cxxstd` to `standard`, for instance?  
  
As usual.. I just wanted things to work hence copy pasted from something else.  
  
> This just generates diffs that have nothing to do with the point of the PR.  
  
LOL I've been doing CI changes **only** for months. The point of the PR was done long, long, ago. ;-)

---

## Comment 6

> Username: pdimov  
> Created_at: 2025-06-26 14:03:18 UTC  
> Url: https://github.com/boostorg/align/pull/20#issuecomment-3008616726  

I merged this before noticing that Appveyor needs a fix too.

---

## Comment 7

> Username: grafikrobot  
> Created_at: 2025-06-26 14:32:16 UTC  
> Url: https://github.com/boostorg/align/pull/20#issuecomment-3008711018  

> I merged this before noticing that Appveyor needs a fix too.  
  
I'll make another PR to try and fix Appveyor.

---
