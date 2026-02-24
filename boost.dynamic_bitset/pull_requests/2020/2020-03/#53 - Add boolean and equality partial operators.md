# #53 Add boolean and equality partial operators [Closed]

> Username: joprodrigues  
> Created at: 2020-03-28 16:34:03 UTC  
> Updated at: 2025-02-11 20:32:29 UTC  
> Closed at: 2025-02-11 20:32:29 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/53  

Create boolean (or, and, xor and sub) and equality operators, that operate  
on two distinct bitsets with different starting points, but same length, in place.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2020-04-14 21:52:04 UTC  
> Updated_at: 2022-05-26 13:26:37 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/53#issuecomment-613699686  

# [Codecov](https://codecov.io/gh/boostorg/dynamic_bitset/pull/53?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#53](https://codecov.io/gh/boostorg/dynamic_bitset/pull/53?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (697c159) into [develop](https://codecov.io/gh/boostorg/dynamic_bitset/commit/d4be7a4fcbee219f5678a3eea81f1a814a7cb5d3?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (d4be7a4) will **increase** coverage by `0.80%`.  
> The diff coverage is `100.00%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/dynamic_bitset/pull/53/graphs/tree.svg?width=650&height=150&src=pr&token=PVG5jth1ez&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/dynamic_bitset/pull/53?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop      #53      +/-   ##  
===========================================  
+ Coverage    95.80%   96.61%   +0.80%       
===========================================  
  Files            5        5                
  Lines          668      826     +158       
===========================================  
+ Hits           640      798     +158       
  Misses          28       28                
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/dynamic_bitset/pull/53?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/dynamic\_bitset/dynamic\_bitset.hpp](https://codecov.io/gh/boostorg/dynamic_bitset/pull/53/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9keW5hbWljX2JpdHNldC9keW5hbWljX2JpdHNldC5ocHA=) | `96.40% <100.00%> (+0.91%)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/dynamic_bitset/pull/53?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/dynamic_bitset/pull/53?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [d4be7a4...697c159](https://codecov.io/gh/boostorg/dynamic_bitset/pull/53?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).

---

## Comment 2

> Username: joprodrigues  
> Created_at: 2020-04-17 06:21:18 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/53#issuecomment-615065835  

My apologies, it's my first time contributing in Github and I've mistakenly added a new commit to this branch (which should be in a different branch), which I have since reverted

---

## Comment 3

> Username: jeking3  
> Created_at: 2020-04-17 10:22:22 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/53#issuecomment-615168577  

So the problem you are trying to solve with this is to allow for operators on subsets of the data; two observations to consider:  
  
1. Code that can operate on a subset should be able to operate on the entire set, but I believe this change introduces alternate code paths for bounded operations.  
  
2. Another possible solution to this problem which I believe would be less change to the class would be to consider allowing a bitset to be created from another as a subset (copies data at an offset and length) or view (references data at an offset and a length, with specific guidelines for the caller to manage the lifetime of the original?).  Then the existing operators would give you the behavior you want by working with subsets more naturally.  
  
An example of #2, in pseudocode:  
  
set one(...)  
set two(...)  
  
Here is how the pull request proposes to do it:  
  
result = one.iand(two, some_offset_into_one, two, some_offset_into_two, length)  
  
Or perhaps instead, think about:  
  
result = one.view(some_offset_into_one, length) & two.view(some_offset_into_two, length)  
  
where view returns a bitset that references the original data at the given offset and length, and then the operator& can be used naturally for an "and" operation.  
  
That said, comments from others that use bitset more regularly would be appreciated.

---

## Comment 4

> Username: joprodrigues  
> Created_at: 2020-04-17 18:10:42 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/53#issuecomment-615389763  

Yes, this change introduces alternate paths. I didn't want to compromise the speed (and correctness!) of the already existing solution.  
Regarding your second suggestion, I will think how can I implement it, since it seems more usable.

---

## Comment 5

> Username: joprodrigues  
> Created_at: 2021-01-07 20:52:26 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/53#issuecomment-756377264  

First, I would like to apologise for the time it took me for this change. I've refactored the code, to create the aforementioned "view", which I called span, due to the similarity with C++20 span.  
  
There are some questions I had while doing this work, and I would like to hear your thoughts on them:  
- when the dynamic_bitset is const, I can't create a const dynamic_bitset_span directly. To solve this, I've created a const_dynamic_bitset_span, which is, of course, trivially converted to const dynamic_bitset_span. However, in some cases I have to explicitly create this new intermediate object.  
- the intersects function has a behaviour different than the other pairwise functions: it operates on the subset where both dynamic_bitsets exist, while other functions only accept equal length dynamic_bitsets  
- in the case of non-member binary operators (for instance dynamic_bitset_span(a) + dynamic_bitset_span(b)), what should be return? Right now, it's (a copy of) the complete underlying dynamic_bitset, but return only the subset operated by the dynamic_bitset_span may also make sense.  
  
Thank you for your attention.  
(And maybe I should also say, thank you to all contributors for this library!)

---

## Comment 6

> Username: jeking3  
> Created_at: 2022-05-03 00:40:30 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/53#issuecomment-1115503549  

@joprodrigues please rebase on develop for a more complete CI suite

---

## Comment 7

> Username: jeking3  
> Created_at: 2024-12-24 14:21:16 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/53#issuecomment-2561178952  

I'd recommend squashing your changes and then rebasing so we can get CI running on this.

---
