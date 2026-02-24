# #1197 [Test] Remove few unused variables [Closed]

> Username: JoeLoser  
> Created at: 2018-07-17 23:22:59 UTC  
> Updated at: 2018-07-23 23:21:06 UTC  
> Closed at: 2018-07-23 23:21:06 UTC  
> Url: https://github.com/boostorg/beast/pull/1197  

Problem:  
- There are a few unused instances of `std::error_code` in some tests.  
  
Solution:  
- Remove the unused instances to silence the unused variable warnings I am getting during builds.  
- Fix some extra whitespace issues in those files I touched with leading whitespace.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2018-07-17 23:47:24 UTC  
> Url: https://github.com/boostorg/beast/pull/1197#issuecomment-405763122  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/1197?src=pr&el=h1) Report  
> Merging [#1197](https://codecov.io/gh/boostorg/beast/pull/1197?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/c4d1bfe7afc16ccf4ec3d6eae93082f810b6d2cb?src=pr&el=desc) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/1197/graphs/tree.svg?height=150&src=pr&token=Gq6MFA9JRF&width=650)](https://codecov.io/gh/boostorg/beast/pull/1197?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop    #1197   +/-   ##  
========================================  
  Coverage    95.52%   95.52%             
========================================  
  Files          113      113             
  Lines        11073    11073             
========================================  
  Hits         10578    10578             
  Misses         495      495  
```  
  
  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/1197?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/1197?src=pr&el=footer). Last update [c4d1bfe...a5ee7a9](https://codecov.io/gh/boostorg/beast/pull/1197?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 2

> Username: vinniefalco  
> Created_at: 2018-07-17 23:52:07 UTC  
> Url: https://github.com/boostorg/beast/pull/1197#issuecomment-405763887  

I appreciate the submission. But I wonder why I am not seeing those...

---

## Comment 3

> Username: JoeLoser  
> Created_at: 2018-07-18 00:43:34 UTC  
> Url: https://github.com/boostorg/beast/pull/1197#issuecomment-405772817  

@vinniefalco valid concern. Does your test matrix test a Clang 6 variant (6.0 or 6.0.1) with -std=c++17? That would match my local build which triggered the warnings.

---

## Comment 4

> Username: vinniefalco  
> Created_at: 2018-07-18 01:05:57 UTC  
> Url: https://github.com/boostorg/beast/pull/1197#issuecomment-405776071  

Hmm....no, but I thought MSVC would warn on unused variables.

---

## Comment 5

> Username: JoeLoser  
> Created_at: 2018-07-18 01:32:56 UTC  
> Updated_at: 2018-07-18 01:33:25 UTC  
> Url: https://github.com/boostorg/beast/pull/1197#issuecomment-405780179  

Right. I digged a bit into the top-level `CMakeLists.txt` along with your `build_and_test.sh` script in the tools folder.  
  
I am a bit surprised MSVC on `/W4` didn't trigger any warnings as [C4101](https://docs.microsoft.com/en-us/cpp/error-messages/compiler-warnings/compiler-warning-level-3-c4101) is enabled on `/W3` or higher.  Similarly, for UNIX builds, the combination of `-Wall -Wextra -Wpedantic` should flag such occurrences (and it does). I'm guessing you don't see unused variable for these `std::error_code` on the UNIX builds with Travis in the logs with top of tree?  
  
As far I can tell, I'd agree with your assessment that you should get a compiler warning here and it is likely an MSVC bug with the lambda nearby of the same type and identifier (though it is a tighter scope in the lambda) -- just a guess. I have not searched around on MSVC bug list.  
  
In any regard, is this Travis error with the Apple Clang variant expected? It looks to be missing a symbol for linking -- unrelated to my specific changes at hand. Does this happen often, is it repeatable, etc?

---

## Comment 6

> Username: vinniefalco  
> Created_at: 2018-07-18 02:11:54 UTC  
> Updated_at: 2018-07-18 02:12:20 UTC  
> Url: https://github.com/boostorg/beast/pull/1197#issuecomment-405786115  

> In any regard, is this Travis error with the Apple Clang variant expected?   
  
It is expected in the sense that I know about it, and it won't go away. It seems to have to do with recent changes in how the Boost libraries are compiled and located, which is confusing Apple clang on Travis for some reason. Two key Boost engineers that might know how to resolve it are unavailable for the next week or two.  
  
Of course, if you can figure it out then you will unlock a very special achievement: my eternal gratitude!

---
