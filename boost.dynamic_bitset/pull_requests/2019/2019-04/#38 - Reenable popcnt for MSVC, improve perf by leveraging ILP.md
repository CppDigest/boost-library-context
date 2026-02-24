# #38 Reenable popcnt for MSVC, improve perf by leveraging ILP [Merged]

> Username: Lastique  
> Created at: 2019-04-13 15:17:17 UTC  
> Updated at: 2019-04-26 21:04:05 UTC  
> Merged at: 2019-04-26 12:55:54 UTC  
> Closed at: 2019-04-26 12:55:54 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/38  

On MSVC and compatible compilers popcnt is now enabled if it is known at  
compile time that the target CPU supports popcnt instruction. Unfortunately,  
MSVC does not have a predefined macro specifically for popcnt, so we have  
to test for AVX instead. `__POPCNT__` is still tested in case clang or user  
defines it.  
  
For MSVC, the 64-bit popcount is now also implemented on 32-bit targets by  
issuing two 32-bit popcnt instructions. For gcc and compatible compilers,  
16-bit `popcount` implementation is provided, which is better than the generic  
`popcount` implementation, but still slower than byte-wise `do_count`.  
  
The `do_count` algorithm implementations have been improved by leveraging  
instruction level parallelism better, which gives 0 to 27% improvement and  
no regressions.  
  
Also made code formatting more consistent and reduced code duplication between  
`do_count` implementations.  
  
This fixes #36

---

## Comment 1

> Username: jeking3  
> Created_at: 2019-04-23 13:27:25 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/38#issuecomment-485802282  

I know about this, it's behind refreshing the CI to clear out the build failure(s).

---

## Comment 2

> Username: jeking3  
> Created_at: 2019-04-23 14:41:19 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/38#issuecomment-485832553  

@Lastique please rebase on develop to clear out the build issues.  Thanks.

---

## Comment 3

> Username: codecov[bot]  
> Created_at: 2019-04-23 15:44:23 UTC  
> Updated_at: 2019-04-25 18:55:15 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/38#issuecomment-485860079  

# [Codecov](https://codecov.io/gh/boostorg/dynamic_bitset/pull/38?src=pr&el=h1) Report  
> Merging [#38](https://codecov.io/gh/boostorg/dynamic_bitset/pull/38?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/dynamic_bitset/commit/c747bec057e30501fe204cc62ea2f285d8dd67ce?src=pr&el=desc) will **increase** coverage by `1.9%`.  
> The diff coverage is `100%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/dynamic_bitset/pull/38/graphs/tree.svg?width=650&token=PVG5jth1ez&height=150&src=pr)](https://codecov.io/gh/boostorg/dynamic_bitset/pull/38?src=pr&el=tree)  
  
```diff  
@@            Coverage Diff             @@  
##           develop      #38     +/-   ##  
==========================================  
+ Coverage    77.15%   79.05%   +1.9%       
==========================================  
  Files            5        5               
  Lines          604      616     +12       
  Branches       211      204      -7       
==========================================  
+ Hits           466      487     +21       
  Misses          24       24               
+ Partials       114      105      -9  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/dynamic_bitset/pull/38?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [...ude/boost/dynamic\_bitset/detail/dynamic\_bitset.hpp](https://codecov.io/gh/boostorg/dynamic_bitset/pull/38/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9keW5hbWljX2JpdHNldC9kZXRhaWwvZHluYW1pY19iaXRzZXQuaHBw) | `97.22% <100%> (+3.67%)` | :arrow_up: |  
| [include/boost/dynamic\_bitset/dynamic\_bitset.hpp](https://codecov.io/gh/boostorg/dynamic_bitset/pull/38/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9keW5hbWljX2JpdHNldC9keW5hbWljX2JpdHNldC5ocHA=) | `77.93% <0%> (+1.69%)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/dynamic_bitset/pull/38?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/dynamic_bitset/pull/38?src=pr&el=footer). Last update [c747bec...b29b053](https://codecov.io/gh/boostorg/dynamic_bitset/pull/38?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 4

> Username: jeking3  
> Created_at: 2019-04-23 17:34:12 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/38#issuecomment-485900997  

At this point I think the failures are legit and not a product of CI.

---

## Comment 5

> Username: Lastique  
> Created_at: 2019-04-23 17:38:52 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/38#issuecomment-485902609  

What failures?

---

## Comment 6

> Username: jeking3  
> Created_at: 2019-04-23 17:41:44 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/38#issuecomment-485903621  

Click through to the Appveyor build.

---

## Comment 7

> Username: Lastique  
> Created_at: 2019-04-23 17:47:54 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/38#issuecomment-485906032  

I'll take a look later, thanks.

---

## Comment 8

> Username: Lastique  
> Created_at: 2019-04-26 12:19:02 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/38#issuecomment-487036881  

The tests have passed.

---

## Review 9 [Commented]

> Username: jeking3  
> Created_at: 2019-04-26 12:51:43 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/dynamic_bitset/pull/38#pullrequestreview-231137851  

📁 include/boost/dynamic_bitset/detail/dynamic_bitset.hpp

```diff
 182 |+             num1 += popcount<ValueType>(*first);
 183 |+             ++first;
 184 |+             num2 += popcount<ValueType>(*first);
```

> Username: jeking3  
> Created_at: 2019-04-26 12:51:43 UTC  
> Updated_at: 2019-04-26 12:53:52 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/38#discussion_r278934372  

It's interesting this improves performance.  Simply by assigning the result to two variables (i.e. registers) that allows them to execute independently even though they are both tied to the advancement of the iterator?  I would have expected to need some sort of hint or intrinsic for that.  Is it the compiler or the CPU doing this?

> Username: jeking3  
> Created_at: 2019-04-26 12:54:26 UTC  
> Updated_at: 2019-04-26 12:54:27 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/38#discussion_r278935285  

I guess on Intel architecture it's CPU based, after some reading.

> Username: Lastique  
> Created_at: 2019-04-26 15:29:57 UTC  
> Updated_at: 2019-04-26 15:29:58 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/38#discussion_r278998500  

The iterator is most likely advanced only once per loop iteration. One of the two iterator dereference operations translates to a memory access with a static offset.  
  
The important optimization here is breaking one dependency chain on the accumulator into two, which allows the CPU to execute popcnt and accumulate instructions in parallel for the two dependency chains. Instruction-level parallelism is a common feature of most modern CPUs that support out-of-order execution. Theoretically, a compiler could generate an equivalent code, but I haven't seen a compiler do this.

> Username: jeking3  
> Created_at: 2019-04-26 16:02:19 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/38#discussion_r279010557  

Potentially any loop unroll without side-effects would be eligible for this sort of thing?

> Username: Lastique  
> Created_at: 2019-04-26 16:11:23 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/38#discussion_r279013554  

Yes, most of the time. You may not get any speedup in some cases, e.g. if your algorithm is memory-bound or the loop body becomes too large for the out-of-order window in the CPU. The latter case is somewhat mitigated by instruction reordering done by compiler or by hand.

> Username: jeking3  
> Created_at: 2019-04-26 16:54:11 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/38#discussion_r279027359  

Sounds like an interesting compiler option if you ask me. :)  Thanks for the explanation.


---

## Review 10 [Commented]

> Username: jeking3  
> Created_at: 2019-04-26 19:10:58 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/dynamic_bitset/pull/38#pullrequestreview-231317859  

📁 include/boost/dynamic_bitset/detail/dynamic_bitset.hpp

```diff
 113 |+         while (value) {
 114 |+             num += count_table<>::table[value & ((1u<<table_width) - 1)];
 115 |+             value >>= table_width;
```

> Username: jeking3  
> Created_at: 2019-04-26 19:10:57 UTC  
> Updated_at: 2019-04-26 19:10:58 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/38#discussion_r279071660  

Coverity flagged this issue:  
  
CID 338011 (#1 of 1): Bad bit shift operation (BAD_SHIFT)  
2. large_shift: In expression value >>= 8U, right shifting value by more than 7 bits always yields zero. The shift amount is 8.  
  
I believe this is intentional in this case, but I want to double-check with you.

> Username: Lastique  
> Created_at: 2019-04-26 19:39:52 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/38#discussion_r279079519  

1. I believe the original code was the same. The change was in the indentation.  
2. The intention of the code is to eventually shift off all set bits, leaving `value` zero. If `value` is less than `int`, it is promoted to `int` before the shift operation, so in no case the shift by 8 bits exceeds the left hand operand capacity. I believe, the error is wrong.

> Username: jeking3  
> Created_at: 2019-04-26 21:04:05 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/38#discussion_r279103213  

I'll mark it as such, thanks.  There's another interesting one in the issues which looks like a real bug.


---
