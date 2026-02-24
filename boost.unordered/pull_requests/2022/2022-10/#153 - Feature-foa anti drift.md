# #153 Feature/foa anti drift [Merged]

> Username: joaquintides  
> Created at: 2022-10-29 18:04:36 UTC  
> Updated at: 2022-10-30 16:06:04 UTC  
> Merged at: 2022-10-30 16:06:04 UTC  
> Closed at: 2022-10-30 16:06:04 UTC  
> Url: https://github.com/boostorg/unordered/pull/153  

Consider this scenario of repetated insert/erase runs:  
```cpp  
boost::unordered_flat_set<int>     s;  
std::uniform_int_distribution<int> dist;  
std::mt19937                       gen(0);  
  
for(int j=0;j<10;++j){  
  // insert  
  gen.seed(j);  
  for(int i=0;i<1720000;++i)s.insert(dist(gen));  
  
  // do some lookup  
  
  // erase  
  gen.seed(j);  
  for(int i=0;i<1720000;++i)s.erase(dist(gen));  
}  
```  
After each insertion run, the average number of hops on unsuccessful lookup is:  
```  
0.233472  
0.490452  
0.768257  
1.07312  
1.41655  
1.80602  
2.24332  
2.73784  
3.32208  
3.98326  
```  
that is, probe lengths grow unboundedly (numbers obtained through instrumentation). This _drift_ phenomenon is due to the fact that overflow bytes can't be reset as elements are erased, so they keep saturating on new insert runs --all non-relocating, open-addressing containers, like Abseil for example, experience this problem.  
The anti-drift mechanism proposed consists in reducing the maximum allowed load when erasing an element that potentially caused an overflow, so that, on the subsequent insert run, rehashing is triggered _before_ hitting the load of the previous run --on average, when the load reaches (previous load − number of erased elements × 0.18). I've re-run the experiment above with anti-drift and the average number of hops stays at 0.23 between runs, as it should.  In this context, rehashing does not grow the bucket array (the same capacity as before is allocated), though we introduce a _hysteresis factor_ to actually grow the array if insert/erase runs oscillate minimally in the vicinity of full load.  
  
Performancewise, anti-drift only affects erasure. [These benchmarks](https://github.com/joaquintides/boost_unordered_benchmarks/tree/foa_anti_drift) show that the overhead ranges from negligible to ~10% in non-SIMD (see "Running erasure").  
  
As for the container's interface, the obvious impact is that rehashing can happen before hitting the theoretical maximum load. We can do two things here (**open question to you**):   
* Leave `load_factor()` as is and let users know the returned valor is a hint and rehashing can happen before expected if there were previous erasures.  
* Change the definition of `load_factor()` to return `(size() + amount detracted to maximum load)/bucket_count()`.

---

## Comment 1

> Username: pdimov  
> Created_at: 2022-10-29 18:31:37 UTC  
> Url: https://github.com/boostorg/unordered/pull/153#issuecomment-1295929522  

I assume you mean `max_load_factor()`, because `load_factor()` returns the current load factor (size / capacity). If so, I prefer option 2, make it return the real max load factor, beyond which reallocation happens.  
  
Since this is a new container, we might consider actually exposing max_load(), which is a more useful query, and typically what users need (how many elements can I insert without reallocation?).

---

## Comment 2

> Username: joaquintides  
> Created_at: 2022-10-29 19:27:14 UTC  
> Url: https://github.com/boostorg/unordered/pull/153#issuecomment-1295946193  

> I assume you mean `max_load_factor()`, because `load_factor()` returns the current load factor (size / capacity). If so, I prefer option 2, make it return the real max load factor, beyond which reallocation happens.  
  
I really meant `load_factor()`, rationale being that slots not recovered count as "load". As for changing the definition of `max_load_factor()`, I don't know if users expect this number to change given its static nature in closed addressing.  
  
Anyway, I like your `max_load()` idea very much: it retains the standard meaning of `load_factor()` and `max_load_factor()` and gives a simple way for users to plan ahead of rehashing.

---

## Comment 3

> Username: pdimov  
> Created_at: 2022-10-29 19:34:33 UTC  
> Url: https://github.com/boostorg/unordered/pull/153#issuecomment-1295948611  

> I really meant `load_factor()`, rationale being that slots not recovered count as "load".  
  
Oh I see. Yes, this makes sense. I still prefer option 2, then, from the two given.

---

## Comment 4

> Username: joaquintides  
> Created_at: 2022-10-29 20:45:10 UTC  
> Url: https://github.com/boostorg/unordered/pull/153#issuecomment-1295969412  

> Oh I see. Yes, this makes sense. I still prefer option 2, then, from the two given.  
  
But we both prefer option 3, do we? (I do)  
  
3. Do not touch `load_factor()` or `max_load_factor()`, and provide `max_load()`

---

## Comment 5

> Username: pdimov  
> Created_at: 2022-10-29 20:48:03 UTC  
> Url: https://github.com/boostorg/unordered/pull/153#issuecomment-1295970190  

Yes, I prefer option 3 too.

---

## Comment 6

> Username: codecov[bot]  
> Created_at: 2022-10-29 22:00:31 UTC  
> Updated_at: 2022-10-30 14:46:42 UTC  
> Url: https://github.com/boostorg/unordered/pull/153#issuecomment-1295995953  

# [Codecov](https://codecov.io/gh/boostorg/unordered/pull/153?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#153](https://codecov.io/gh/boostorg/unordered/pull/153?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (a81d785) into [develop](https://codecov.io/gh/boostorg/unordered/commit/772e1e7fa0577d3c61416669b8ac1513af6ab4d6?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (772e1e7) will **increase** coverage by `0.05%`.  
> The diff coverage is `85.71%`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/unordered/pull/153/graphs/tree.svg?width=650&height=150&src=pr&token=ZqRPZlJZ5N&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/unordered/pull/153?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #153      +/-   ##  
===========================================  
+ Coverage    97.61%   97.66%   +0.05%       
===========================================  
  Files           81       81                
  Lines        11826    12003     +177       
===========================================  
+ Hits         11544    11723     +179       
+ Misses         282      280       -2       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/unordered/pull/153?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/unordered/unordered\_flat\_map.hpp](https://codecov.io/gh/boostorg/unordered/pull/153/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91bm9yZGVyZWQvdW5vcmRlcmVkX2ZsYXRfbWFwLmhwcA==) | `99.00% <ø> (ø)` | |  
| [include/boost/unordered/unordered\_flat\_set.hpp](https://codecov.io/gh/boostorg/unordered/pull/153/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91bm9yZGVyZWQvdW5vcmRlcmVkX2ZsYXRfc2V0LmhwcA==) | `99.38% <ø> (ø)` | |  
| [include/boost/unordered/detail/foa.hpp](https://codecov.io/gh/boostorg/unordered/pull/153/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91bm9yZGVyZWQvZGV0YWlsL2ZvYS5ocHA=) | `97.93% <85.71%> (+0.44%)` | :arrow_up: |  
| [test/unordered/rehash\_tests.cpp](https://codecov.io/gh/boostorg/unordered/pull/153/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC91bm9yZGVyZWQvcmVoYXNoX3Rlc3RzLmNwcA==) | `100.00% <0.00%> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/unordered/pull/153?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/unordered/pull/153?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [772e1e7...a81d785](https://codecov.io/gh/boostorg/unordered/pull/153?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
