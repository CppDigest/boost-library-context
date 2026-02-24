# #27 Add set/reset/flip functions to change sequences [Merged]

> Username: Izaron  
> Created at: 2018-08-23 20:24:16 UTC  
> Updated at: 2018-11-03 03:14:32 UTC  
> Merged at: 2018-10-12 12:07:01 UTC  
> Closed at: 2018-10-12 12:07:01 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/27  

Neither boost::dynamic_bitset nor vanilla std::bitset have the `fill()` function, and I propose to introduce this.  
The point of the function is being used instead of loops with calling the `set(pos)` function.  
  
Let's consider an example where we want either to set **1024** _consecutive_ bits or to reset them (taking as the fact that we don't change the whole bitset, only its part).  
Using a cycle we do it using, well, **1024** operations. Though we could have done it using maximum 1024/64+1=**17** operations (considering the blocks as 64-bit integers) - setting the whole blocks between the first and the last bit, and optionally setting a suffix or a prefix of the first/last block respectively.  
  
I also updated docs, tests. Yeah, the algorithm can look a bit complicated, and there are 2 helper functions added, but there's nothing hard at all.  
  
Any suggestions, notes on possible performance improvement, arguments meaning (we change here [first; last], but can change [first;last) as it's more STL-like style); maybe naming too? I guess `fill(size_type first, size_type last, bool val = true);` could be named `set(size_type first, size_type last, bool val = true);` as well.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2018-08-24 02:02:45 UTC  
> Updated_at: 2018-08-31 22:26:09 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/27#issuecomment-415628146  

# [Codecov](https://codecov.io/gh/boostorg/dynamic_bitset/pull/27?src=pr&el=h1) Report  
> Merging [#27](https://codecov.io/gh/boostorg/dynamic_bitset/pull/27?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/dynamic_bitset/commit/b944aa300867faff61befb9f6de38b360f01aecf?src=pr&el=desc) will **increase** coverage by `1.35%`.  
> The diff coverage is `90.56%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/dynamic_bitset/pull/27/graphs/tree.svg?width=650&token=PVG5jth1ez&height=150&src=pr)](https://codecov.io/gh/boostorg/dynamic_bitset/pull/27?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop      #27      +/-   ##  
===========================================  
+ Coverage    75.41%   76.76%   +1.35%       
===========================================  
  Files            5        5                
  Lines          541      594      +53       
  Branches       198      210      +12       
===========================================  
+ Hits           408      456      +48       
- Misses          23       24       +1       
- Partials       110      114       +4  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/dynamic_bitset/pull/27?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/dynamic\_bitset/dynamic\_bitset.hpp](https://codecov.io/gh/boostorg/dynamic_bitset/pull/27/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9keW5hbWljX2JpdHNldC9keW5hbWljX2JpdHNldC5ocHA=) | `76.24% <90.56%> (+1.48%)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/dynamic_bitset/pull/27?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/dynamic_bitset/pull/27?src=pr&el=footer). Last update [b944aa3...f536fcd](https://codecov.io/gh/boostorg/dynamic_bitset/pull/27?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 2

> Username: jeking3  
> Created_at: 2018-08-29 02:55:47 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/27#issuecomment-416807106  

This change is fine, however I think calling it set() makes more sense, and adding reset(f, l) and perhaps flip(f, l) so that all the bit manipulators can operate either on a single bit or a range of bits would complete the desired goal to modify sequences efficiently.  As for the meaning of `last` as you discussed, I'm wondering if `first_bit, num_bits` makes sense instead of `first_bit, last_bit`.  Then all instantiations of set forward to one implementation that would hopefully be optimal for all cases: `set() -> set(0, num_bits)`, and `set(bit) -> set(bit, 1)`.  Just a thought - not sure it would work out that way but worth thinking about.

---

## Comment 3

> Username: Izaron  
> Created_at: 2018-08-31 00:09:07 UTC  
> Updated_at: 2018-08-31 00:11:41 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/27#issuecomment-417507356  

@jeking3 I like your thoughts and agree with most of them.  
  
Using relative last bit positions instead of absolute is done in std::string and it's logical to do the same here.  
  
I decided to not call `set(bit) -> set(but, 1)` and similar, because the common case (any borders) takes some calculations and is more slower than just changing a bit, or setting all the bits to 1. Moreover current `set` functions are tiny.  
  
But I have a serious doubt connected with implicit conversions from int to bool.  
If a programmer paid no attention to warnings and/or had to code really fast, he could write this:  
```  
int okay = 42;  
/* Transforming 'okay' */  
db.set(pos, okay);  
```  
The last line was supposed to be `db.set(pos, okay != 0)` according to the current state, but having a new function `dynamic_bitset& set(size_t n, size_t len, bool value = true)` makes it into `db.set(pos, okay, true)` implicitly, which seems to me a serious thing...   
  
Other things seem fine to me, I'd make reset() and flip() with tests as well, after some time.

---

## Comment 4

> Username: Izaron  
> Created_at: 2018-08-31 21:20:18 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/27#issuecomment-417792086  

So, I've added some commits and there are three new range functions... With tests, docs, without code doubling. Anyone, feel free to comment them!

---

## Review 5 [Commented]

> Username: jeking3  
> Created_at: 2018-10-21 16:37:57 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/dynamic_bitset/pull/27#pullrequestreview-166792036  

📁 dynamic_bitset.html

```diff
 199 |     dynamic_bitset <a href="#op-sr">operator&gt;&gt;</a>(size_type n) const;
 200 | 
 201 |+     dynamic_bitset&amp; <a href="#set3">set</a>(size_type n, size_type len, bool val = true);
```

> Username: jeking3  
> Created_at: 2018-10-21 16:37:57 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/27#discussion_r226869343  

This has led to ambiguity.  See https://github.com/rdkit/rdkit/issues/2128.  We need to resolve this.  I will open an issue.

> Username: jeking3  
> Created_at: 2018-11-03 03:14:31 UTC  
> Updated_at: 2018-11-03 03:14:32 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/27#discussion_r230543556  

This needs to be resolved by Nov 7 or it will be reverted for the Boost 1.69.0 beta.


---
