# #120 Improve field count typical case performance [Merged]

> Username: runer112  
> Created at: 2023-01-18 22:44:19 UTC  
> Updated at: 2024-10-10 03:21:35 UTC  
> Merged at: 2024-10-09 11:12:40 UTC  
> Closed at: 2024-10-09 11:12:40 UTC  
> Url: https://github.com/boostorg/pfr/pull/120  

The tightest upper bound one can specify on the number of fields in a struct is `sizeof(type) * CHAR_BIT`. So this was previously used when performing a binary search for the field count. This upper bound is extremely loose when considering a typical large struct, which is more likely to contain a relatively small number of relatively large fields rather than the other way around. The binary search range being multiple orders of magnitude larger than necessary wouldn't have been a significant issue if each test was cheap, but they're not. Testing a field count of N costs O(N) memory and time. As a result, the initial few steps of the binary search may be prohibitively expensive.  
  
The primary optimization introduced by these changes is to use unbounded binary search, a.k.a. exponential search, instead of the typically loosely bounded binary search. This produces a tight upper bound (within 2x) on the field count to then perform the binary search with.  
  
As an upside of this change, the compiler-specific limit placed on the upper bound on the field count to stay within compiler limits could be removed.

---

## Comment 1

> Username: runer112  
> Created_at: 2023-01-18 22:55:06 UTC  
> Updated_at: 2023-01-18 22:59:46 UTC  
> Url: https://github.com/boostorg/pfr/pull/120#issuecomment-1396202005  

This issue was originally noticed when some source files in a large project seemed to be consuming a suspiciously large amount of memory (and also time). After some digging, the culprit was eventually nailed down as field count detection.  
  
Before these changes, compiling one such source file with clang 14 peaked at 1.5 GB of memory usage. After these changes, compiling the same file peaked at 617 MB. These numbers include the memory usage of compiling everything else as well, which is why the after-fix number is still relatively large. The memory usage attributable just to field counting probably went from something like 1 GB to something at least one or two orders of magnitude less.

---

## Comment 2

> Username: apolukhin  
> Created_at: 2023-01-19 13:36:13 UTC  
> Updated_at: 2023-01-19 13:36:58 UTC  
> Url: https://github.com/boostorg/pfr/pull/120#issuecomment-1396985750  

The PR fails on msvc-14.1 with `c1xx: fatal error C1060: compiler is out of heap space` error.  
  
I'd recommend to try another approach: just change the `detect_fields_count_greedy` to fill an `bool init_succeeded[Last]`. This could be done in one go, by getting an index sequence from Last, and applying it to the function that returns true/false, depending on the construction success. After that a simple loop in constexpr could finish the job.  
  
Something like that:  
```  
constexpr size_t detect_fields_count_greedy(index_sequence<Indexes...>) {  
bool init_succeeded[Last] = { is_aggr_initable<T, Indexes≥(), ... };  
for ( i = Last - 1; i > 0; --i) if init_succeeded [i] return i;  
}  
```

---

## Comment 3

> Username: runer112  
> Created_at: 2023-01-24 00:13:51 UTC  
> Updated_at: 2023-01-24 18:45:11 UTC  
> Url: https://github.com/boostorg/pfr/pull/120#issuecomment-1401186250  

In the last CI run, 15 tasks failed with a compiler is out of heap space error. With the jobs running in parallel, it's hard to determine which tasks failed due to their own excessive memory usage and which were well-behaved, but a victim of running when another task consumed all the available memory. I pushed a temporary commit that I believe should disable testing in parallel. Could you please approve the CI build? Hopefully this will give me enough info to be able to diagnose the real issue, after which I can revert the CI config change.  
  
 - [x] Revert the CI config change.  
  
Regarding your suggestion, maybe I'm not understanding it correctly, but I don't see how it would help with performance. The crux of the performance issue is that the check of whether a type is constructible with N arguments, `enable_if_constructible_helper_t` (SFINAE), costs O(N) memory and time. In your suggestion, I believe this would make initializing `init_succeeded` cost O(Last^2) time and O(Last) memory.  
  
My proposed changes aim to minimize the overall cost by minimizing the sum of all N checked. They effectively replace `sizeof(T)` with just the result (field count) in asymptotic performance analysis, which may be substantially lesser. Comparing to the current code (don't trust the current comments):  
  
Case                             | Memory Before | Memory After | Time Before                   | Time After  
-------------------------------- | ------------- | ------------ | ----------------------------- | -----------------------  
`T` is an array                  | O(1)          | O(1)         | O(1)                          | O(1)  
`T` is default-constructible     | O(sizeof(T))  | O(result)    | O(sizeof(T) * log(sizeof(T))) | O(result * log(result))  
`T` is not default-constructible | O(sizeof(T))  | O(result)    | O(sizeof(T)^2)                | O(result^2)

---

## Comment 4

> Username: runer112  
> Created_at: 2023-01-24 22:12:28 UTC  
> Updated_at: 2023-01-30 21:54:15 UTC  
> Url: https://github.com/boostorg/pfr/pull/120#issuecomment-1402760744  

The excessive compile time/memory usage issues that were causing failures have been fixed.  
  
The central issue was that the `static_assert` preconditions didn't actually prevent the compiler from trying to expand the field counting templates, which sometimes expanded indefinitely. This was fixed by dummying the field counting dispatch to do basically nothing (count the number of fields in an `int[1]` instead, which is trivially 1) if any precondition is not met.

---

## Comment 5

> Username: runer112  
> Created_at: 2023-01-30 21:44:06 UTC  
> Updated_at: 2023-01-30 21:46:16 UTC  
> Url: https://github.com/boostorg/pfr/pull/120#issuecomment-1409391991  

@apolukhin Is there anything more you'd like me to address?  
  
The AppVeyor build succeeds now, and does so roughly 5% faster than before despite there being 3 new tests.

---

## Comment 6

> Username: apolukhin  
> Created_at: 2023-02-03 09:18:39 UTC  
> Updated_at: 2023-02-03 09:19:58 UTC  
> Url: https://github.com/boostorg/pfr/pull/120#issuecomment-1415440892  

Sorry, I've misread you for the first time.  
  
So here's how I see your changes (please fix me if I'm wrong):  
  
T is default-constructible: you do exponentioal search for upper bound of fields count. It takes log(fields_count) + 1. After that you do a binary search that takes log(log(fields_count) + 1). The final complexity is log(fields_count) + 1 + log(log(fields_count) + 1).  
  
The current implementation starts the binary search from sizeof(type) * CHAR_BIT. However, that CHAR_BIT multiplication is not necessary - we do not need to know the eact count of bitfields. Instead of that the binary search could work with sizeof(type)+1, and if we get the maximum value, then the type has bitfields.  
  
Here comes the math. Your algorithm is better when  
log(sizeof(type)+1) > log(fields_count) + 1 + log(log(fields_count) + 1)  
  
which is   
log(sizeof(type)+1) > log(fields_count) + log(2) + log(log(fields_count) + 1)  
  
which is   
sizeof(type)+1 > fields_count*2 * (log(fields_count) + 1)  
  
sizeof(type) is equal to fields_count*avg_field_size. It gives us  
  
fields_count*avg_field_size+1 > fields_count*2 * (log(fields_count) + 1)  
  
Which is  
avg_field_size+1/fields_count > 2 * log(fields_count) + 2  
  
For fields count 16 the avg_field_size should be about 10 to make your algorithm better. For fields count 256 the avg_field_size should be about 18 to make your algorithm better.  
  
For aggregates of ints, chronos, pointers or size_ts existing algorithm performs better. For aggregates of strings and vectors your algorithm performs better than the existing.   
  
I'd rather call it a tie. But the CHAR_BITS multiplocation should be removed.  
  
T is not defsult constructible: your approach is defenetly superior. I'm worried about the cases, when the whole type is not aggregate initializable, because in that case I think your algo would run as long as the RAM os not exhausted and no diagnostic will be provided. Probably it is the reason, why github CI fails.  
  
I'm also worried about compiler idiosyncrasies. Not all the compilers are listed in CI, so I'd rather stick to the existing, well tested algorithm, if it does not make a noticeable difference.  
  
**Here's the plan:**  
* remove the CHAR_BITS multiplication  
* for the second case: do the linear search for first non default constructible T, and then use the existing binary search with sizeof(T)+1 upper limit

---

## Comment 7

> Username: runer112  
> Created_at: 2023-02-03 23:33:02 UTC  
> Updated_at: 2023-02-03 23:34:34 UTC  
> Url: https://github.com/boostorg/pfr/pull/120#issuecomment-1416529425  

Your understanding of the approach used in these changes is correct: exponential search followed by binary search. However, your performance analysis only counts "steps". Critically, it does not factor in the cost of checking at each step whether the type is constructible with N arguments, which is O(N) memory and time.  
  
Factoring this in to the default-constructible case, the exponential search worst case costs O(1 + 2 + 4 + ... + fields_count + 2 * fields_count) = O(4 * fields_count) to establish bounds separated only by a factor of 2. The best case costs O(1 + 2 + 4 + ... + fields_count + 1) = O(2 * fields_count).  
  
Without exponential search and ignoring the possibility of bitfields, we start with a binary search over [0, sizeof(T)]. To reach bounds separated only by a factor of 2, in the best case of an average field size in [1, 2] bytes, this only requires one check costing O(sizeof(T) / 2). This ranges from O(field_count / 2) to O(field_count), which is 1/4 to 1/2 of exponential search's best case cost.  
  
However, the favorable comparison fades rather quickly. Once the average field size passes 4 bytes, reachng bounds separated only by a factor of 2 requires (at least) three checks costing O(sizeof(T) / 2 + sizeof(T) / 4 + sizeof(T) / 8) = O(7/8 * sizeof(T)). The field count must be less than sizeof(T) / 4, so in terms of field count, the cost is at least O(7/8 * 4 * field_count) = O(7/2 * field_count). This is already nearly the worst case cost of exponential search, and it only gets worse for larger average field sizes.  
  
In the worst case of a single field, the cost is O(sizeof(T) / 2 + sizeof(T) / 4 + sizeof(T) / 8 + ... 1) = O(sizeof(T)). This is sizeof(T) / 4 times exponential search's worst case cost. The cost factor is nearly unbounded as it simply grows with sizeof(T).  
  
Considering that the best case cost factor of using binary search only is 1/4 and the worst case cost factor is nearly unbounded, I believe that that alone should be enough reason to use exponential search. Additionally, it seems that exponential search may be faster in "average" use, as evidenced by the AppVeyor builds with these changes being roughly 5% faster than builds without. And as a reminder, really poor performance cases with binary search aren't just a theoretical possibility that won't happen in practice. I went through the effort to make and propose these changes specifically because I ran into such a poorly performing case where these changes made a huge difference (https://github.com/boostorg/pfr/pull/120#issuecomment-1396202005).  
  
Regarding the latest CI failure, I haven't had much time to look into it yet. I was hoping that it wouldn't be necessary to do this because it feels unclean, but perhaps it's wise to add a sanity check to the exponential search to halt it if it somehow exceeds `sizeof(T) * CHAR_BIT`.

---

## Comment 8

> Username: apolukhin  
> Created_at: 2023-02-04 08:22:22 UTC  
> Url: https://github.com/boostorg/pfr/pull/120#issuecomment-1416695549  

> Critically, it does not factor in the cost of checking at each step whether the type is constructible with N arguments, which is O(N) memory and time.  
  
It should not take O(N). `std::make_index_sequence` was a bottleneck in so many cases, that the compiler developers made it an intrinsic (for example https://github.com/microsoft/STL/blob/73924c1920af92899f7582cd904ea819b9db35bc/stl/inc/type_traits#L34). So getting the indexes is O(1), variadic pack expansion is also close to O(1), `ubiq_*ref_constructor` is not a template and its constructors do not consume time/memory. As a result the check should take about O(1).  
  
Please, check that your compiler supports the builtin and that it is properly detected in https://github.com/boostorg/pfr/blob/28bd7f541f7a7632f4fe52e30d06a121e7cb1f65/include/boost/pfr/detail/make_integer_sequence.hpp

---

## Comment 9

> Username: runer112  
> Created_at: 2023-02-07 15:46:45 UTC  
> Updated_at: 2024-09-05 19:13:47 UTC  
> Url: https://github.com/boostorg/pfr/pull/120#issuecomment-1420993441  

Even if `std::make_index_sequence`/`std::make_integer_sequence` is implemented intrinsically, the cost is not constant. The cost increases with the size of the sequence.  
  
Here's a demonstration of recent versions of clang, gcc, and msvc all running out of resources (whether self-limited or host-limited time or memory) trying to evaluate `std::make_integer_sequence<int, 10000000>()`: https://godbolt.org/z/KqhfxbK4z. Dropping to 1 million, I see clang and gcc succeed, but only after multiple seconds. Dropping to 100 thousand, I see msvc finally succeed, and clang and gcc succeed in less than a second.

---

## Comment 10

> Username: runer112  
> Created_at: 2024-09-05 16:23:28 UTC  
> Url: https://github.com/boostorg/pfr/pull/120#issuecomment-2332159961  

I noticed that someone else (@rressi-at-globus) appears to have discovered these changes and found the changes to be useful enough to make their own attempt to get these changes upstreamed with #179. I resynchronized my branch with `develop` to hopefully allow this PR to proceed, now with third-party corroboration that these changes are beneficial.

---

## Comment 11

> Username: runer112  
> Created_at: 2024-09-05 19:37:48 UTC  
> Updated_at: 2024-09-05 23:40:46 UTC  
> Url: https://github.com/boostorg/pfr/pull/120#issuecomment-2332496989  

I re-ran the tests that I previously ran in https://github.com/boostorg/pfr/pull/120#issuecomment-1420993441 to once again demonstrate that the cost of `std::make_index_sequence` increases with the size of the sequence, now with more recent versions of clang, gcc, and msvc just in case something got optimized. My findings are the same as before: the cost of `std::make_index_sequence` increases with the size of the sequence; I suspect O(N).  
  
1. https://godbolt.org/z/PKqes5h1M: `std::make_index_sequence<10'000'000>` gave me the same result across multiple retries for all 3 compilers tested: failure due to resource exhaustion.  
2. https://godbolt.org/z/9jEGEG776: `std::make_index_sequence<1'000'000>` gave me best-case results of clang completing in 0.9 seconds, gcc completing in 2.6 seconds, and msvc failing due to resource exhaustion.  
3. https://godbolt.org/z/PKqes5h1M: `std::make_index_sequence<100'000>` gave me best-case results of clang completing in 0.3 seconds, gcc completing in 0.5 seconds, and msvc completing in 1.3 seconds.

---

## Comment 12

> Username: runer112  
> Created_at: 2024-09-05 23:38:43 UTC  
> Updated_at: 2024-09-06 16:00:11 UTC  
> Url: https://github.com/boostorg/pfr/pull/120#issuecomment-2332903082  

@apolukhin: Here are direct reponses to your comment, https://github.com/boostorg/pfr/pull/120#issuecomment-1415440892. I apologize for not doing this earlier.  
  
---  
  
> I'm worried about the cases, when the whole type is not aggregate initializable, because in that case I think your algo would run as long as the RAM os not exhausted and no diagnostic will be provided.  
  
You were right, there was a problem. I added tests for a default-constructible type accepting 0 or more arguments and a non-default-constructible type accepting 1 or more arguments in e80f7aa951676d5c25d3d264b87d63eebe505d5f. These tests should now pass with the changes from dd1ae1c907a6181ff2eff32cf1091450b9950df1.  
  
---  
  
> I'd rather stick to the existing, well tested algorithm, if it does not make a noticeable difference  
  
It does make a noticeable difference for large types.  
  
Practical evidence:  
  
1. https://github.com/boostorg/pfr/pull/120#issuecomment-1396202005  
2. https://github.com/boostorg/pfr/pull/120#issuecomment-2332159961  
  
Theoretical evidence:  
  
1. https://github.com/boostorg/pfr/pull/120#issuecomment-1416529425  
2. https://github.com/boostorg/pfr/pull/120#issuecomment-2332496989  
  
---  
  
> **Here's the plan:**  
>   
> * remove the CHAR_BITS multiplication  
> * for the second case: do the linear search for first non default constructible T, and then use the existing binary search with sizeof(T)+1 upper limit  
  
As you're aware, the current implementation performs binary search with an upper bound of `sizeof(T) * CHAR_BIT`. For large types, the first few iterations could carry a significant cost, perhaps even resulting in resource exhaustion. This could maybe warrant removing the multiplication by `CHAR_BIT` at the cost of losing support for types with dense bitfields (average field size < 1 byte).  
  
The proposed implementation would perform binary search with an upper bound that is no more than twice the field count. This would not carry any unnecessary significant cost. In this regard, removing the multiplication by `CHAR_BIT` should not be warranted.  
  
There is however a preexisting case of concern: large types that are not aggregate-initializable in environments that do not provide `std::is_aggregate`. With both the existing and the proposed implementation, this may result in searching all the way up to `sizeof(T) * CHAR_BIT` just to eventually encounter resource exhaustion or produce an error stating that the type is not aggregate-initializable. But `std::is_aggregate` is required by the C++17 standard, environments without it will only become rarer with time, and using a non-aggregate type with pfr seems to be a misuse to begin with? So with the proposed implementation, removing the multiplication by `CHAR_BIT` only to mitigate poor performance in the case of misuse with a dated C++ environment, while also at the cost of losing support for types with dense bitfields, doesn't seem worth it to me. But if I've forgotten to account for another problematic case or if you otherwise disagree and still want to the multiplication by `CHAR_BIT` to be removed, let me know.

---

## Comment 13

> Username: rressi-at-globus  
> Created_at: 2024-09-06 06:30:08 UTC  
> Updated_at: 2024-09-06 06:31:07 UTC  
> Url: https://github.com/boostorg/pfr/pull/120#issuecomment-2333339790  

I have exported this contribution as a patch for the *vcpkg*''s port of this component.  
  
The patch is curently targeting *boost-pfr version 1.85.0*, and is under review here:  
- https://github.com/microsoft/vcpkg/pull/40823

---

## Comment 14

> Username: apolukhin  
> Created_at: 2024-09-13 12:44:28 UTC  
> Url: https://github.com/boostorg/pfr/pull/120#issuecomment-2348873144  

Still fails the tests

---

## Comment 15

> Username: runer112  
> Created_at: 2024-09-13 19:37:56 UTC  
> Url: https://github.com/boostorg/pfr/pull/120#issuecomment-2350027961  

I'm looking at the output of the failed runs now, but there seems to be a lot of unstructured text to comb through... Any tips as to what to look at?

---

## Comment 16

> Username: runer112  
> Created_at: 2024-09-13 20:02:53 UTC  
> Updated_at: 2024-09-13 21:37:08 UTC  
> Url: https://github.com/boostorg/pfr/pull/120#issuecomment-2350067192  

After my best attempts to search for failures in the log files, I could not spot any test failures, but I did spot a couple of Boost Inspection Report problems. These should now be addressed. Hopefully this was all that was resulting in jobs failing?

---

## Comment 17

> Username: coveralls  
> Created_at: 2024-09-19 07:12:02 UTC  
> Updated_at: 2024-10-09 09:15:10 UTC  
> Url: https://github.com/boostorg/pfr/pull/120#issuecomment-2360170226  

## Pull Request Test Coverage Report for [Build 11220902440](https://coveralls.io/builds/70243443)  
  
  
### Details  
  
* **0** of **0**   changed or added relevant lines in **0** files are covered.  
* No unchanged relevant lines lost coverage.  
* Overall coverage remained the same at **100.0%**  
  
---  
  
  
  
|  Totals | [![Coverage Status](https://coveralls.io/builds/70243443/badge)](https://coveralls.io/builds/70243443) |  
| :-- | --: |  
| Change from base [Build 10872623956](https://coveralls.io/builds/69803827): |  0.0% |  
| Covered Lines: | 407 |  
| Relevant Lines: | 407 |  
  
---  
##### 💛  - [Coveralls](https://coveralls.io)

---

## Comment 18

> Username: runer112  
> Created_at: 2024-09-20 01:24:01 UTC  
> Url: https://github.com/boostorg/pfr/pull/120#issuecomment-2362525892  

Bleh, one more failing CI job. It's late for me today, but I can investigate that tomorrow.  
  
Also, it appears that the coverage report didn't work because I merged `develop` back into this branch rather than rebasing. Would you like me to rebase instead?

---

## Comment 19

> Username: runer112  
> Created_at: 2024-09-20 18:57:18 UTC  
> Url: https://github.com/boostorg/pfr/pull/120#issuecomment-2364344458  

I have implemented a workaround for an MSVC issue that caused failures in [windows (msvc-14.3, 20,latest, 64, windows-2022, -j1)](https://github.com/boostorg/pfr/actions/runs/10855330620/job/30358979387?pr=120#logs). It's hard for me to tell from the output if this was the only issue, but I'm hopeful that it was.

---

## Comment 20

> Username: runer112  
> Created_at: 2024-09-20 23:12:49 UTC  
> Updated_at: 2024-09-20 23:30:47 UTC  
> Url: https://github.com/boostorg/pfr/pull/120#issuecomment-2364738508  

I've made additional improvements that should remove any unnecessary trial initializations of array types. This means that the cost of calculating the field count of an array is now constant, as was probably initially intended. This is tested here:  
  
https://github.com/runer112/pfr/blob/cbc57cc28758c36c77b3fbe4e4ef7fb8e98f5ae8/test/core/run/huge_count.cpp#L53  
  
I've also finally added testing of the core improvement that this PR provides, which is that the cost of calculating the field count of an object is not related to the object's size:  
  
https://github.com/runer112/pfr/blob/cbc57cc28758c36c77b3fbe4e4ef7fb8e98f5ae8/test/core/run/huge_count.cpp#L55-L56  
  
I've tested the changes manually on a couple of the test files with a few different compiler configurations, but this is nowhere near as much testing as CI performs. It's possible that a CI run will reveal one or two issues that I did not find, but I'm hopeful that it won't. Either way, I appreciate you approving CI runs.

---

## Comment 21

> Username: runer112  
> Created_at: 2024-09-30 18:02:40 UTC  
> Url: https://github.com/boostorg/pfr/pull/120#issuecomment-2383845669  

The latest CI run revealed 3 embarrassingly simple issues that slipped in last week while making robustness and testing improvements. I have pushed commits to address these issues.

---

## Review 22 [Changes requested]

> Username: apolukhin  
> Created_at: 2024-10-03 09:09:38 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/pfr/pull/120#pullrequestreview-2345028898  

📁 include/boost/pfr/detail/fields_count.hpp

```diff
  16 | import std;
  17 | #else
  18 |+ #include <algorithm>    // min
```

> Username: apolukhin  
> Created_at: 2024-10-03 08:56:02 UTC  
> Updated_at: 2024-10-03 09:09:38 UTC  
> Url: https://github.com/boostorg/pfr/pull/120#discussion_r1785872376  

Avoid including this header. It is very big and slows down compilation

> Username: runer112  
> Created_at: 2024-10-04 22:20:49 UTC  
> Updated_at: 2024-10-04 22:24:33 UTC  
> Url: https://github.com/boostorg/pfr/pull/120#discussion_r1788384024  

[Avoid including \<algorithm\>](https://github.com/boostorg/pfr/pull/120/commits/ae8ce8dfe32842250e84123a5c7db2e5a04e8fac)

---

📁 include/boost/pfr/detail/fields_count.hpp

```diff
  97 |+ 
  98 | template <class T, std::size_t N>
  82 |- struct is_aggregate_initializable_n {
```

> Username: apolukhin  
> Created_at: 2024-10-03 09:05:06 UTC  
> Updated_at: 2024-10-03 09:09:38 UTC  
> Url: https://github.com/boostorg/pfr/pull/120#discussion_r1785884498  

There were changes in the C++ standard about aggregates and aggregate initialization. I then the old name is more precise

> Username: runer112  
> Created_at: 2024-10-04 22:19:46 UTC  
> Updated_at: 2024-10-04 22:24:33 UTC  
> Url: https://github.com/boostorg/pfr/pull/120#discussion_r1788383577  

[Restore "is_aggregate_initializable_n" name](https://github.com/boostorg/pfr/pull/120/commits/d222d982f9e8481e48d45cc39ec6ce58cff14cb1)

---

📁 include/boost/pfr/detail/fields_count.hpp

```diff
 252 |+ template <class T, std::size_t Begin, std::size_t I>
 253 |+ constexpr auto fields_count_upper_bound(long, long) noexcept
 254 |+     -> std::enable_if_t<(Begin + I > fields_count_upper_bound_loose<T>()), std::size_t>
```

> Username: apolukhin  
> Created_at: 2024-10-03 09:06:36 UTC  
> Updated_at: 2024-10-03 09:09:38 UTC  
> Url: https://github.com/boostorg/pfr/pull/120#discussion_r1785886308  

Add namespace to function call to avoid ADL

> Username: runer112  
> Created_at: 2024-10-04 22:21:10 UTC  
> Updated_at: 2024-10-04 22:24:33 UTC  
> Url: https://github.com/boostorg/pfr/pull/120#discussion_r1788384185  

[Prevent ADL throughout fields_count.hpp](https://github.com/boostorg/pfr/pull/120/commits/ff14705fac6bf2c1f697fe776f87e171b147d622)

---

📁 include/boost/pfr/detail/fields_count.hpp

```diff
 257 |+         !detail::is_initializable<T, fields_count_upper_bound_loose<T>() + 1>(1L),
 258 |+         "====================> Boost.PFR: Types with user specified constructors (non-aggregate initializable types) are not supported.");
 259 |+     return fields_count_upper_bound_loose<T>();
```

> Username: apolukhin  
> Created_at: 2024-10-03 09:06:42 UTC  
> Updated_at: 2024-10-03 09:09:38 UTC  
> Url: https://github.com/boostorg/pfr/pull/120#discussion_r1785886434  

Add namespace to function call to avoid ADL

> Username: runer112  
> Created_at: 2024-10-04 22:21:13 UTC  
> Updated_at: 2024-10-04 22:24:33 UTC  
> Url: https://github.com/boostorg/pfr/pull/120#discussion_r1788384203  

[Prevent ADL throughout fields_count.hpp](https://github.com/boostorg/pfr/pull/120/commits/ff14705fac6bf2c1f697fe776f87e171b147d622)

---

📁 include/boost/pfr/detail/fields_count.hpp

```diff
 311 |+ template <class T, std::size_t Begin = 1>
 312 |+ constexpr auto fields_count_lower_bound_unbounded(long, size_t_<0> = {}) noexcept
 313 |+     -> std::enable_if_t<(Begin >= fields_count_upper_bound_loose<T>()), std::size_t>
```

> Username: apolukhin  
> Created_at: 2024-10-03 09:07:17 UTC  
> Updated_at: 2024-10-03 09:09:38 UTC  
> Url: https://github.com/boostorg/pfr/pull/120#discussion_r1785887161  

Add namespace to function call to avoid ADL

> Username: runer112  
> Created_at: 2024-10-04 22:21:16 UTC  
> Updated_at: 2024-10-04 22:24:33 UTC  
> Url: https://github.com/boostorg/pfr/pull/120#discussion_r1788384218  

[Prevent ADL throughout fields_count.hpp](https://github.com/boostorg/pfr/pull/120/commits/ff14705fac6bf2c1f697fe776f87e171b147d622)

---

📁 include/boost/pfr/detail/fields_count.hpp

```diff
 314 |+ {
 315 |+     static_assert(
 316 |+         detail::is_initializable<T, fields_count_upper_bound_loose<T>()>(1L),
```

> Username: apolukhin  
> Created_at: 2024-10-03 09:07:25 UTC  
> Updated_at: 2024-10-03 09:09:38 UTC  
> Url: https://github.com/boostorg/pfr/pull/120#discussion_r1785887302  

Add namespace to function call to avoid ADL

> Username: runer112  
> Created_at: 2024-10-04 22:21:19 UTC  
> Updated_at: 2024-10-04 22:24:33 UTC  
> Url: https://github.com/boostorg/pfr/pull/120#discussion_r1788384236  

[Prevent ADL throughout fields_count.hpp](https://github.com/boostorg/pfr/pull/120/commits/ff14705fac6bf2c1f697fe776f87e171b147d622)

---

📁 include/boost/pfr/detail/fields_count.hpp

```diff
 316 |+         detail::is_initializable<T, fields_count_upper_bound_loose<T>()>(1L),
 317 |+         "====================> Boost.PFR: Type must be aggregate initializable.");
 318 |+     return fields_count_upper_bound_loose<T>();
```

> Username: apolukhin  
> Created_at: 2024-10-03 09:07:30 UTC  
> Updated_at: 2024-10-03 09:09:38 UTC  
> Url: https://github.com/boostorg/pfr/pull/120#discussion_r1785887385  

Add namespace to function call to avoid ADL

> Username: runer112  
> Created_at: 2024-10-04 22:21:22 UTC  
> Updated_at: 2024-10-04 22:24:33 UTC  
> Url: https://github.com/boostorg/pfr/pull/120#discussion_r1788384251  

[Prevent ADL throughout fields_count.hpp](https://github.com/boostorg/pfr/pull/120/commits/ff14705fac6bf2c1f697fe776f87e171b147d622)

---

📁 include/boost/pfr/detail/fields_count.hpp

```diff
 322 |+ constexpr std::size_t fields_count_lower_bound_unbounded(int, size_t_<0> = {}) noexcept
 323 |+ {
 324 |+     constexpr std::size_t last = (std::min)(Begin * 2, fields_count_upper_bound_loose<T>()) - 1;
```

> Username: apolukhin  
> Created_at: 2024-10-03 09:08:03 UTC  
> Updated_at: 2024-10-03 09:09:38 UTC  
> Url: https://github.com/boostorg/pfr/pull/120#discussion_r1785888040  

Add namespace to function call to avoid ADL... add namespaces to all the function calls in this file

> Username: runer112  
> Created_at: 2024-10-04 22:21:27 UTC  
> Updated_at: 2024-10-04 22:24:33 UTC  
> Url: https://github.com/boostorg/pfr/pull/120#discussion_r1788384294  

[Prevent ADL throughout fields_count.hpp](https://github.com/boostorg/pfr/pull/120/commits/ff14705fac6bf2c1f697fe776f87e171b147d622)

---

📁 include/boost/pfr/detail/fields_count.hpp

```diff
 357 |     using type = std::remove_cv_t<T>;
 358 | 
 359 |+     constexpr bool precondition1 = detail::is_complete<type>::value;
```

> Username: apolukhin  
> Created_at: 2024-10-03 09:09:21 UTC  
> Updated_at: 2024-10-03 09:09:38 UTC  
> Url: https://github.com/boostorg/pfr/pull/120#discussion_r1785889844  

The asserts with the condition right in them produce a more readable error messages. And they are shorter in code.  
  
Please restore the old way of reporting

> Username: runer112  
> Created_at: 2024-10-04 04:39:30 UTC  
> Updated_at: 2024-10-04 05:36:02 UTC  
> Url: https://github.com/boostorg/pfr/pull/120#discussion_r1787145188  

The goal of this change is that if any precondition is not satisfied, then the field counting  and postcondition assertions are skipped, preventing any later errors from muddying up the output. And this does seem to work. The "If there's no other failed static asserts then something went wrong" assertion was no longer triggered in the latest CI run of this PR, https://github.com/boostorg/pfr/actions/runs/11111419938?pr=120, except for in the one compiler job that encountered a real failure (which I'll fix ASAP).  
  
However, if you find it desirable to keep the assertion expression in compiler error messages despite the custom assertion failure messages, then I will restore this.

> Username: runer112  
> Created_at: 2024-10-04 22:22:11 UTC  
> Updated_at: 2024-10-04 22:24:33 UTC  
> Url: https://github.com/boostorg/pfr/pull/120#discussion_r1788384579  

- [Revert "Prevent unnecessary triggering of last-resort assertion"](https://github.com/boostorg/pfr/pull/120/commits/13f29c635f89d17a65abde5e59a05e7d4dfb9861)  
- [Revert "Add complete type precondition and condition evaluation on preconditions"](https://github.com/boostorg/pfr/pull/120/commits/ab3621c45d4cee56fd70c61a9ae162fcd7608906)  
- [Add back complete type precondition](https://github.com/boostorg/pfr/pull/120/commits/d6677deeaf802726a36e875fdb5707d618a0d533)

> Username: apolukhin  
> Created_at: 2024-10-07 11:44:31 UTC  
> Url: https://github.com/boostorg/pfr/pull/120#discussion_r1790067113  

Oh, then the change is fine, but let's make it in a separate PR


---

## Review 23 [Commented]

> Username: apolukhin  
> Created_at: 2024-10-03 09:37:03 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/pfr/pull/120#pullrequestreview-2345117875  

📁 include/boost/pfr/detail/fields_count.hpp

```diff
 302 |- #if defined(_MSC_VER) && (_MSC_VER <= 1920)
 303 |-     // Workaround for msvc compilers. Versions <= 1920 have a limit of max 1024 elements in template parameter pack
 304 |-     constexpr std::size_t max_fields_count = (sizeof(type) * CHAR_BIT >= 1024 ? 1024 : sizeof(type) * CHAR_BIT);
```

> Username: apolukhin  
> Created_at: 2024-10-03 09:37:03 UTC  
> Url: https://github.com/boostorg/pfr/pull/120#discussion_r1785926591  

Move this workaround to fields_count_upper_bound_loose

> Username: runer112  
> Created_at: 2024-10-04 04:49:27 UTC  
> Updated_at: 2024-10-04 05:31:58 UTC  
> Url: https://github.com/boostorg/pfr/pull/120#discussion_r1787150738  

I tested this out with the preexisting pfr code. It succeeds for structs with at most 1022 (note: not quite 1024!) fields, but for structs with more fields, produces the error "C1202: recursive type or function dependency context too complex". So I assume it's ok for the new code to succeed up to slightly less than 1024 fields (maybe not exactly 1022) and produce the same error or a similar error for more fields?

> Username: runer112  
> Created_at: 2024-10-04 22:22:43 UTC  
> Updated_at: 2024-10-04 22:26:40 UTC  
> Url: https://github.com/boostorg/pfr/pull/120#discussion_r1788384775  

- [Test field count for a type with 1k fields](https://github.com/boostorg/pfr/pull/120/commits/5978e520a1defc7acfd0a7e8927207dbcbd0b288)  
- [Stop at expected compiler limitation values during exponential search](https://github.com/boostorg/pfr/pull/120/commits/68e6abaa255c0d594da725a961ccad4c467dad85)


---

## Review 24 [Commented]

> Username: apolukhin  
> Created_at: 2024-10-03 13:16:46 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/pfr/pull/120#pullrequestreview-2345592317  

📁 include/boost/pfr/detail/fields_count.hpp

```diff
 260 |+ }
 261 |+ 
 262 |+ template <class T, std::size_t Begin, std::size_t I>
```

> Username: apolukhin  
> Created_at: 2024-10-03 13:16:46 UTC  
> Url: https://github.com/boostorg/pfr/pull/120#discussion_r1786208388  

Looks like Begin is always 0 in all the fields_count_upper_bound functions. Just remove that template parameter

> Username: runer112  
> Created_at: 2024-10-04 03:21:06 UTC  
> Updated_at: 2024-10-04 05:32:52 UTC  
> Url: https://github.com/boostorg/pfr/pull/120#discussion_r1787071358  

`Begin` will not be 0 when `T` is not default initializable, it will be the size of the smallest valid initializer: https://github.com/runer112/pfr/blob/c5424df5af7cea789f2cfb3da8842def0a47ede4/include/boost/pfr/detail/fields_count.hpp#L346-L352

> Username: apolukhin  
> Created_at: 2024-10-07 11:43:29 UTC  
> Url: https://github.com/boostorg/pfr/pull/120#discussion_r1790065697  

Ok, then it is fine


---

## Review 25 [Commented]

> Username: runer112  
> Created_at: 2024-10-04 05:31:58 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/pfr/pull/120#pullrequestreview-2347019519  

Thank you for your code review, @apolukhin. I have read all of your comments. I replied to 3 comments that I wasn't sure how to proceed for. All of the other comments make sense and I will address them ASAP.

---

## Review 26 [Changes requested]

> Username: apolukhin  
> Created_at: 2024-10-07 11:49:06 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/pfr/pull/120#pullrequestreview-2351716320  

Looks almost perfectly!  
  
Added a few comments

📁 include/boost/pfr/detail/fields_count.hpp

```diff
  34 |+ ///////////////////// min without including <algorithm>
  35 |+ template <class T>
  36 |+ constexpr const T &min(const T &a, const T &b) {
```

> Username: apolukhin  
> Created_at: 2024-10-07 11:46:43 UTC  
> Updated_at: 2024-10-07 11:49:06 UTC  
> Url: https://github.com/boostorg/pfr/pull/120#discussion_r1790070382  

Just remove all the references and pass std::size_t by value

> Username: runer112  
> Created_at: 2024-10-07 16:54:27 UTC  
> Updated_at: 2024-10-07 18:09:00 UTC  
> Url: https://github.com/boostorg/pfr/pull/120#discussion_r1790574253  

The current signature and behavior match [`std::min` (1)](https://en.cppreference.com/w/cpp/algorithm/min). In a constant-evaluated context, which should be the case for all operations in this file, there will be no runtime difference.  
  
If you still want this to pass and return by value, let me know and I will make the change. But if doing so, I believe I should also un-template the function and make it strictly accept and return `std::size_t` so the function can't be accidentally invoked with some other type that may be incorrect to pass/return by value.

---

📁 include/boost/pfr/detail/fields_count.hpp

```diff
 216 |+ constexpr std::size_t fields_count_compiler_limitation_next(std::size_t n) noexcept {
 217 |+ #if defined(_MSC_VER) && (_MSC_VER <= 1920)
 218 |+     if (n < 1024)
```

> Username: apolukhin  
> Created_at: 2024-10-07 11:48:09 UTC  
> Updated_at: 2024-10-07 11:49:06 UTC  
> Url: https://github.com/boostorg/pfr/pull/120#discussion_r1790072477  

Wrong comparison?

> Username: runer112  
> Created_at: 2024-10-07 17:29:25 UTC  
> Updated_at: 2024-10-07 18:09:59 UTC  
> Url: https://github.com/boostorg/pfr/pull/120#discussion_r1790618770  

Given an initializer count `n`, this function returns the next largest value that doesn't exceed the next expected compiler limitation. The minimum of this value and the next exponential step value is used for the next trial initializer count ([code](https://github.com/runer112/pfr/blob/4adba126d26925f8cad96c8bee32a804c6b72a0c/include/boost/pfr/detail/fields_count.hpp#L273-L280)). This should result in normally following exponential steps, except when this step would cross over an expected compiler limitation, it will instead take a "pitstop" at the next largest value that doesn't exceed the next expected compiler limitation.  
  
This ensures that an expected compiler limitation will not be crossed until a trial at the limit value has been performed and has determined that larger trial values are necessary to determine the result. If this occurs, then the limit may as well be crossed. This is expected to result in a compiler error due to the limitation, but in case the expectation was incorrect for some reason, then the exponential search will continue and may make it far enough to successfully produce a result.  
  
Following the logic I explained above, this comparison seems to be correct to me. Is there a specific concern you have? Perhaps this function just needs a better name or explanation in the preceding comment?


---

## Review 27 [Commented]

> Username: runer112  
> Created_at: 2024-10-07 18:08:02 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/pfr/pull/120#pullrequestreview-2352486573  

I have replied to your new comments and will await your responses. I have also pushed a simple fix for what seems to be the only remaining issue identified by CI: [Address violation of Boost min/max guidelines](https://github.com/boostorg/pfr/pull/120/commits/fe6f150a32c3ed6e6e56ae42b650ad3ac4e7952b).

---

## Comment 28

> Username: apolukhin  
> Created_at: 2024-10-09 12:00:02 UTC  
> Url: https://github.com/boostorg/pfr/pull/120#issuecomment-2402123329  

Awesome work!  
  
Many thanks for it!

---

## Comment 29

> Username: runer112  
> Created_at: 2024-10-10 03:21:34 UTC  
> Url: https://github.com/boostorg/pfr/pull/120#issuecomment-2403859208  

> Awesome work!  
>   
> Many thanks for it!  
  
And thank you for hanging in there with me through the long process to ensure that these changes meet the high standards expected for a widely-used library.  
  
Although I hope this should never happen, if any concerns are raised about this set of changes in the future, feel free to contact me for support.

---
