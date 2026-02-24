# #244 Fix: make partition work for forward ranges [Merged]

> Username: mkaravel  
> Created at: 2015-03-06 09:19:14 UTC  
> Updated at: 2015-04-28 13:47:50 UTC  
> Merged at: 2015-04-06 12:12:43 UTC  
> Closed at: 2015-04-06 12:12:43 UTC  
> Url: https://github.com/boostorg/geometry/pull/244  

The current `partition<>` implementation, the one or two input collections passed are assumed internally to be random access containers (to be precise, it is required that the collection's `operator[]` is defined).  
In fact, this is requirement is a bit too strict. `partition<>` can easily work even for forward ranges, and this is the focus of this PR.  
  
In this PR, `partition<>` has been modified so as to work for forward ranges. This eliminates among other things the need for the range to define `operator[]`. The idea behind the re-implementation is as follows: instead of storing and keeping track of indices of collection items (these are the indices that are used as arguments in `operator[]` calls), iterators to collection items are stored and manipulated. The `index_vector_type` is consistently replaced by a vector of iterators (possibly of two different types if the two input collections are different). Accessing the collection items in this context simply amounts to de-referencing the iterators stored in the iterator vectors. The only operators used with respect to the iterators of the input collections are `operator++` and `operator*`.  
  
On the practical side: this allows to use partition with ranges which are not random access. Two such cases are ranges coming from BG's `point_iterator<>` and `segment_iterator<>` which are bidirectional iterators.  
  
All algorithm unit tests pass with the re-designed `partition<>`.

---

## Comment 1

> Username: barendgehrels  
> Created_at: 2015-03-06 19:21:10 UTC  
> Updated_at: 2015-03-07 15:34:59 UTC  
> Url: https://github.com/boostorg/geometry/pull/244#discussion_r25969779  

So we can remove this argument

---

## Comment 2

> Username: barendgehrels  
> Created_at: 2015-03-06 19:21:24 UTC  
> Updated_at: 2015-03-07 15:34:59 UTC  
> Url: https://github.com/boostorg/geometry/pull/244#discussion_r25969803  

Idem

---

## Comment 3

> Username: barendgehrels  
> Created_at: 2015-03-06 19:24:59 UTC  
> Updated_at: 2015-03-07 15:34:59 UTC  
> Url: https://github.com/boostorg/geometry/pull/244#discussion_r25970124  

A vector of iterators. What is the size compared to integers? Can you do a performance comparison test too with old/new?

---

## Comment 4

> Username: barendgehrels  
> Created_at: 2015-03-06 19:25:57 UTC  
> Updated_at: 2015-03-06 19:58:52 UTC  
> Url: https://github.com/boostorg/geometry/pull/244#issuecomment-77619637  

I wonder why you call this "Fix". It is obviously not a fix, but an improvement, making it more generic

---

## Comment 5

> Username: barendgehrels  
> Created_at: 2015-03-06 19:26:11 UTC  
> Url: https://github.com/boostorg/geometry/pull/244#issuecomment-77619675  

Please postpone this until after 1.58

---

## Comment 6

> Username: barendgehrels  
> Created_at: 2015-03-06 19:27:52 UTC  
> Url: https://github.com/boostorg/geometry/pull/244#issuecomment-77619989  

I also wonder why you call this "reimplement" and "redesign". Anyway, does not matter, just wondering - it is just changing a part of the implementation

---

## Comment 7

> Username: mkaravel  
> Created_at: 2015-03-06 19:35:28 UTC  
> Updated_at: 2015-03-07 15:34:59 UTC  
> Url: https://github.com/boostorg/geometry/pull/244#discussion_r25971072  

Yes, we can. Will do it.

---

## Comment 8

> Username: mkaravel  
> Created_at: 2015-03-06 19:35:42 UTC  
> Updated_at: 2015-03-07 15:34:59 UTC  
> Url: https://github.com/boostorg/geometry/pull/244#discussion_r25971099  

Same here. Will do it.

---

## Comment 9

> Username: mkaravel  
> Created_at: 2015-03-06 19:36:06 UTC  
> Updated_at: 2015-03-07 15:34:59 UTC  
> Url: https://github.com/boostorg/geometry/pull/244#discussion_r25971139  

I will try to. More to come.

---

## Comment 10

> Username: mkaravel  
> Created_at: 2015-03-06 19:44:15 UTC  
> Url: https://github.com/boostorg/geometry/pull/244#issuecomment-77622688  

- About "Fix": partly lack of creativity in the naming, and partly that it "fixes" the "shortcoming" of working for random access ranges only.  
- About 1.58: no problem: my usage case is difference and intersection of pointlike/linear geometries, so it is not really needed for 1.58.  
- About "reimplement" and "redesign": partly lack of creativity and imagination for naming what I did, and partly the fact that I use iterators instead of indices (which in my mind is a design choice). Honestly, I never intended to mean that this is my/new design or my/new implementation; it seemed more natural to call what I did a "redesign" or a "reimplementation" rather than a "change". But this is just my own opinion, and I do not have strong feelings about it. What is important for me here is the added functionality.

---

## Comment 11

> Username: mkaravel  
> Created_at: 2015-03-06 19:48:00 UTC  
> Updated_at: 2015-03-07 15:34:59 UTC  
> Url: https://github.com/boostorg/geometry/pull/244#discussion_r25972175  

I remember now why I decided to keep it: in order to allow for the possibility to pass it, if we want at a later stage, to any of the policies visitors and used in the algorithm.  
If you think this is too much I can remove it.

---

## Comment 12

> Username: barendgehrels  
> Created_at: 2015-03-06 19:56:28 UTC  
> Updated_at: 2015-03-07 15:34:59 UTC  
> Url: https://github.com/boostorg/geometry/pull/244#discussion_r25972913  

OK, I see. For Expand I don't think it will be useful, so IMO you can remove it - we can always readd it if really needed

---

## Comment 13

> Username: mkaravel  
> Created_at: 2015-03-06 19:58:13 UTC  
> Updated_at: 2015-03-07 15:34:59 UTC  
> Url: https://github.com/boostorg/geometry/pull/244#discussion_r25973054  

Okay. Will remove it.

---

## Comment 14

> Username: barendgehrels  
> Created_at: 2015-03-06 19:58:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/244#issuecomment-77625064  

OK, thanks for the explanations. Good to hear you want to use it for point like differences.

---

## Comment 15

> Username: mkaravel  
> Created_at: 2015-03-06 20:00:23 UTC  
> Url: https://github.com/boostorg/geometry/pull/244#issuecomment-77625358  

It is for pointlike-linear differences. Pointlike-pointlike are already implemented in a different way (sorting and binary search; I did benchmarks then, and it was faster that way compared to using partition).

---

## Comment 16

> Username: barendgehrels  
> Created_at: 2015-03-06 20:04:35 UTC  
> Url: https://github.com/boostorg/geometry/pull/244#issuecomment-77625984  

Sure, pointlike and linear. We still have to make partition so fast that it can compare to std::sort ....

---

## Comment 17

> Username: mkaravel  
> Created_at: 2015-03-07 15:36:59 UTC  
> Url: https://github.com/boostorg/geometry/pull/244#discussion_r25996831  

Done. See commit ee1e0e0.

---

## Comment 18

> Username: mkaravel  
> Created_at: 2015-03-07 15:43:27 UTC  
> Url: https://github.com/boostorg/geometry/pull/244#discussion_r25996883  

Regarding the size: it depends on the size of the iterator of course.  
  
My understanding is that for the existing use cases we should not see a difference in the size of the vector: what was passed was a random access container/range and an integer index; now what I pass is an iterator to the random access container/range, and I expect the size of this iterator to be the same as the size of the index.  
  
It is true that if you pass a possibly more complicated iterator (like `point_iterator<>` or `segment_iterator<>`) then the size of the vector may be larger; on the other hand such usages where not possible before, and in that respect I do not see an issue with respect to size.  
  
I will investigate this in more detail as part of the performance comparison.

---

## Comment 19

> Username: mkaravel  
> Created_at: 2015-03-07 18:40:15 UTC  
> Url: https://github.com/boostorg/geometry/pull/244#discussion_r25998285  

One more update on this: on my computer the size of `std::size_t` and the size of iterators of, for example, `bg::multi_point<>` and `bg::multi_linestring<>`, are both 8 bytes.  
So my understanding is that for all existing usages, there is no overhead in using a vector of iterators instead of a vector of `std::size_t`'s.

---

## Comment 20

> Username: mkaravel  
> Created_at: 2015-03-09 08:15:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/244#issuecomment-77814255  

## **_Performance comparison_**  
  
### **Setup**  
  
I tried the following scenario: I considered random points in a `[0,1] x [0,1]` square, seen as multipoints. For these multipoints I called partition. The visitor passed is not doing something very meaningful, but still I wanted it to do something to avoid any possible optimizations by the compiler. What it does is that for the points in each bottom-most level box, it computes the minimum distance among them. I considered running the old and new implementation of partition either for a single multipoint (one-collection version of partition) or for two multipoints (two-collections version of partition).  
  
See the following files (the partition implementations were given different names and put in different namespace so that I can use both of them in the same program):  
- Comparison/timing program: https://github.com/mkaravel/geometry/blob/performance-partition/performance/algorithms/partition/multipoint_multipoint.cpp  
- Old partition implementation: https://github.com/mkaravel/geometry/blob/performance-partition/performance/algorithms/partition/partition_old.hpp  
- New partition implementation: https://github.com/mkaravel/geometry/blob/performance-partition/performance/algorithms/partition/partition_new.hpp  
  
### **Compilation**  
  
The comparison program was compiled with `clang++` version 3.4 with the flags `-O2` and `-DNDEBUG`.  
  
### **Timings**  
  
The following two tables show the elapsed times for the old and new partition implementation with respect to the size of the multipoint(s) passed.  
Times are in seconds. `Boost.Timer` has been used for performing the timings. Smallest time is shown in bold-face.  
  
| _Multipoint size_ | _Old_ | _New_ |  
| :-: | --: | --: |  
| 10^3 | 0.000618 | **0.000585** |  
| 10^4 | 0.002773 | **0.002450** |  
| 10^5 | **0.035126** | 0.036622 |  
| 10^6 | 0.431059 | **0.394527** |  
| 10^7 | 5.861435 | **5.680503** |  
  
| _Multipoint  # 1 size_ | _Multipoint  # 2 size_ | _Old_ | _New_ |  
| :-: | :-: | --: | --: |  
| 10^3 | 10^3 | 0.001106 | **0.000997** |  
| 10^4 | 10^4 | 0.005468 | **0.005185** |  
| 10^5 | 10^5 | 0.076411 | **0.065967** |  
| 10^6 | 10^6 | 0.849864 | **0.813731** |  
| 10^7 | 10^7 | 12.670377 | **12.326983** |

---

## Comment 21

> Username: mkaravel  
> Created_at: 2015-03-09 08:44:37 UTC  
> Url: https://github.com/boostorg/geometry/pull/244#issuecomment-77817270  

My understanding is that there is no show-stopper for this PR.  
  
Since we have decided to leave it for boost 1.59, please just comment that the PR is indeed okay, and we can merge once our develop branch is open for 1.59

---

## Comment 22

> Username: barendgehrels  
> Created_at: 2015-03-09 18:48:29 UTC  
> Updated_at: 2015-03-09 18:48:55 UTC  
> Url: https://github.com/boostorg/geometry/pull/244#issuecomment-77917220  

The timings look convincing, thanks for this. We already earlier noticed that changing from index operator to iterators is measurably faster. So good that that is confirmed here, also in this case.  
So yes, we can merge it for 1.59

---
