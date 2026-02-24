# #144 Rehashing Conformity [Merged]

> Username: cmazakas  
> Created at: 2022-09-02 18:17:29 UTC  
> Updated at: 2022-09-13 21:53:15 UTC  
> Merged at: 2022-09-13 21:53:15 UTC  
> Closed at: 2022-09-13 21:53:15 UTC  
> Url: https://github.com/boostorg/unordered/pull/144  

Update the rehash() and reserve() functions so that they behave similarly to libstdc++ and libc++, i.e. rehashing should grow and shrink the allocation accordingly while still respecting the max load factor

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2022-09-02 21:21:03 UTC  
> Updated_at: 2022-09-13 19:34:47 UTC  
> Url: https://github.com/boostorg/unordered/pull/144#issuecomment-1235906925  

# [Codecov](https://codecov.io/gh/boostorg/unordered/pull/144?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#144](https://codecov.io/gh/boostorg/unordered/pull/144?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (14a604b) into [develop](https://codecov.io/gh/boostorg/unordered/commit/dea6ce164c04c2258dada82c9f01d3ad45b2910e?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (dea6ce1) will **decrease** coverage by `0.04%`.  
> The diff coverage is `97.57%`.  
  
> :exclamation: Current head 14a604b differs from pull request most recent head 9a9b8e0. Consider uploading reports for the commit 9a9b8e0 to get more accurate results  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/unordered/pull/144/graphs/tree.svg?width=650&height=150&src=pr&token=ZqRPZlJZ5N&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/unordered/pull/144?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #144      +/-   ##  
===========================================  
- Coverage    97.45%   97.41%   -0.05%       
===========================================  
  Files           75       75                
  Lines        10369    10509     +140       
===========================================  
+ Hits         10105    10237     +132       
- Misses         264      272       +8       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/unordered/pull/144?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [test/unordered/noexcept\_tests.cpp](https://codecov.io/gh/boostorg/unordered/pull/144/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC91bm9yZGVyZWQvbm9leGNlcHRfdGVzdHMuY3Bw) | `95.68% <81.48%> (-4.32%)` | :arrow_down: |  
| [include/boost/unordered/detail/fca.hpp](https://codecov.io/gh/boostorg/unordered/pull/144/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91bm9yZGVyZWQvZGV0YWlsL2ZjYS5ocHA=) | `99.62% <100.00%> (+<0.01%)` | :arrow_up: |  
| [include/boost/unordered/detail/implementation.hpp](https://codecov.io/gh/boostorg/unordered/pull/144/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91bm9yZGVyZWQvZGV0YWlsL2ltcGxlbWVudGF0aW9uLmhwcA==) | `98.92% <100.00%> (-0.25%)` | :arrow_down: |  
| [test/unordered/rehash\_tests.cpp](https://codecov.io/gh/boostorg/unordered/pull/144/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC91bm9yZGVyZWQvcmVoYXNoX3Rlc3RzLmNwcA==) | `100.00% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/unordered/pull/144?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/unordered/pull/144?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [2b61fbb...9a9b8e0](https://codecov.io/gh/boostorg/unordered/pull/144?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Review 2 [Commented]

> Username: joaquintides  
> Created_at: 2022-09-04 10:51:26 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/unordered/pull/144#pullrequestreview-1095648975  

* Consider using `std::floor` instead of `floor`.  
* Why are you using `floor` in `recalculate_max_load` anyway? The `static_cast<std::size_t>(f)` bit already does the truncation, right?   
* You're using `std::size_t` sometimes and `size_type` some other times.  
* `implementation.hpp#L3454`: you're introducing `size_type bc = num_buckets` when you could have continued using `num_buckets` (which is no longer referred to).  
* In the context of `table`, `prime_fmod_size` is only used by rehash. This is breaking encapsulation, as the growing policy is an implementation detail of `grouped_bucket_array`. Consider adding a static memfun like `bucket_count_for(num_buckets)` in `grouped_bucket_array`. This memfun should also cover the case `num_buckets`=0 (returning 0).  
* The calculation of `min_required` is incorrect due to the added 1.0, as `x.rehash(x.bucket_count())`, which must do nothing by definition, will *grow* the number of buckets when the size is exactly the maximum load. I understand this is here to cover the case `mlf_`=∞, but I'm afraid you need to reformulate. Please add a test case before fixing.  
* I recommend trying to rewrite `rehash` so that you don't have a `size_==0` branch. The code could look something like this:  
  
```cpp  
std::size_t min_buckets(std::size_t num_elements,float mlf)  
{  
  std::size_t m=static_cast<size_type>(std::ceil(static_cast<float>(num_elements)/mlf));  
  if(!m&&num_elements)m=1; // mlf==inf  
  return m;  
}  
  
template <typename Types>  
inline void table<Types>::rehash(std::size_t num_buckets)  
{  
  num_buckets=(std::max)(  
    buckets_.bucket_count_for(num_buckets),  
    min_buckets(size_,mlf_));  
  if(num_buckets!=this->bucket_count())this->rehash_impl(num_buckets);  
}  
```  
  
* `implementation.hpp#L3475`: why the `size_ == 0` in the conditional? If `num_elements > 0 && num_buckets == 0`, that implies that `mlf_`=∞, in which case 1 is the right choice for the requested `num_buckets` regardless of whether there are elements in the container or not.  
* The standard [says](http://eel.is/c++draft/unord.req#general-236) that `reserve` is exactly `rehash(ceil(num_elements / max_load_factor()))`, which is `rehash(0)` when `mlf_`=∞. We're not following this requirement as we're issuing `rehash(1)` here, which is IMHO the correct behavior --if `num_elements`>0, I certainly want at least one bucket. As I see it, this is a defect in the standard and you may want to file a DR: either the definition of `reserve` should be changed or else ∞ be banned as a valid argument to `max_load_factor`.  
* This new behavior is not documented, and it should as it goes beyond the standard requirements.

---

## Comment 3

> Username: joaquintides  
> Created_at: 2022-09-07 09:22:06 UTC  
> Url: https://github.com/boostorg/unordered/pull/144#issuecomment-1239134931  

Looks good to me. There's a lot of commented-out code in `rehash_tests.cpp`. After a private conversation with Chris, I understand that doc updates will arrive in a different PR --I'd rather have it all in one single PR, but this other approach works as well.

---

## Comment 4

> Username: pdimov  
> Created_at: 2022-09-07 09:29:44 UTC  
> Url: https://github.com/boostorg/unordered/pull/144#issuecomment-1239143181  

The GHA and Appveyor failures are transient, but the Drone gcc 4.7 one looks legitimate.

---

## Comment 5

> Username: joaquintides  
> Created_at: 2022-09-07 09:41:11 UTC  
> Url: https://github.com/boostorg/unordered/pull/144#issuecomment-1239155878  

Ouch, didn't see that one, thought they were all transient stuff.

---
