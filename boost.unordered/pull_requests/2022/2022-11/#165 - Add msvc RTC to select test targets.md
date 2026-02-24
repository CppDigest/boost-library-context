# #165 Add msvc RTC to select test targets [Merged]

> Username: cmazakas  
> Created at: 2022-11-19 16:15:53 UTC  
> Updated at: 2022-11-28 17:09:19 UTC  
> Merged at: 2022-11-28 17:09:15 UTC  
> Closed at: 2022-11-28 17:09:15 UTC  
> Url: https://github.com/boostorg/unordered/pull/165  

Fixes https://github.com/boostorg/unordered/issues/151

---

## Comment 1

> Username: pdimov  
> Created_at: 2022-11-19 16:21:12 UTC  
> Url: https://github.com/boostorg/unordered/pull/165#issuecomment-1320917805  

As discussed on Slack, instead of changing the Jamfile, /RTCc and /arch:IA32 need to be separate CI jobs.

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2022-11-19 18:35:25 UTC  
> Updated_at: 2022-11-23 03:00:12 UTC  
> Url: https://github.com/boostorg/unordered/pull/165#issuecomment-1320942646  

# [Codecov](https://codecov.io/gh/boostorg/unordered/pull/165?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#165](https://codecov.io/gh/boostorg/unordered/pull/165?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (20ea4a7) into [develop](https://codecov.io/gh/boostorg/unordered/commit/108d4535e030d56a4e11102e9194d92d662ea913?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (108d453) will **not change** coverage.  
> The diff coverage is `100.00%`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/unordered/pull/165/graphs/tree.svg?width=650&height=150&src=pr&token=ZqRPZlJZ5N&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/unordered/pull/165?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #165   +/-   ##  
========================================  
  Coverage    97.65%   97.65%             
========================================  
  Files           83       83             
  Lines        12034    12034             
========================================  
  Hits         11752    11752             
  Misses         282      282             
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/unordered/pull/165?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/unordered/detail/foa.hpp](https://codecov.io/gh/boostorg/unordered/pull/165/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91bm9yZGVyZWQvZGV0YWlsL2ZvYS5ocHA=) | `97.93% <ø> (ø)` | |  
| [include/boost/unordered/detail/prime\_fmod.hpp](https://codecov.io/gh/boostorg/unordered/pull/165/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91bm9yZGVyZWQvZGV0YWlsL3ByaW1lX2Ztb2QuaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/unordered/unordered\_flat\_set.hpp](https://codecov.io/gh/boostorg/unordered/pull/165/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91bm9yZGVyZWQvdW5vcmRlcmVkX2ZsYXRfc2V0LmhwcA==) | `99.40% <ø> (ø)` | |  
| [include/boost/unordered/unordered\_set.hpp](https://codecov.io/gh/boostorg/unordered/pull/165/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91bm9yZGVyZWQvdW5vcmRlcmVkX3NldC5ocHA=) | `99.64% <ø> (ø)` | |  
| [test/unordered/deduction\_tests.cpp](https://codecov.io/gh/boostorg/unordered/pull/165/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC91bm9yZGVyZWQvZGVkdWN0aW9uX3Rlc3RzLmNwcA==) | `100.00% <ø> (ø)` | |  
| [test/unordered/prime\_fmod\_tests.cpp](https://codecov.io/gh/boostorg/unordered/pull/165/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC91bm9yZGVyZWQvcHJpbWVfZm1vZF90ZXN0cy5jcHA=) | `94.04% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/unordered/pull/165?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/unordered/pull/165?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [ee8f2b9...20ea4a7](https://codecov.io/gh/boostorg/unordered/pull/165?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Review 3 [Commented]

> Username: pdimov  
> Created_at: 2022-11-22 15:33:19 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/unordered/pull/165#pullrequestreview-1190288637  

📁 .github/workflows/ci.yml

```diff
 253 |           B2_CXXSTD: ${{matrix.cxxstd}}
 254 |           B2_ADDRESS_MODEL: ${{matrix.addrmd}}
 255 |+           B2_CXXFLAGS: ${{ (matrix.toolset == 'msvc-14.3' && matrix.cxxstd == '14' && ( matrix.addrmd == '64' && '/RTCc' || '"/RTCc /arch:IA32"')) || '' }}
```

> Username: pdimov  
> Created_at: 2022-11-22 15:33:19 UTC  
> Url: https://github.com/boostorg/unordered/pull/165#discussion_r1029487634  

You need to add `cxxflags` and `defines` to the matrix, instead of hardcoding checks here.


---

## Review 4 [Commented]

> Username: pdimov  
> Created_at: 2022-11-22 15:34:10 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/unordered/pull/165#pullrequestreview-1190290192  

📁 test/unordered/prime_fmod_tests.cpp

```diff
 156 |     boost::uint64_t f = rng();
 158 |-     boost::uint32_t d = static_cast<uint32_t>(rng());
 157 |+     boost::uint32_t d = rng() & 0xffffffffu;
```

> Username: pdimov  
> Created_at: 2022-11-22 15:34:10 UTC  
> Url: https://github.com/boostorg/unordered/pull/165#discussion_r1029488698  

This probably still needs the static_cast.


---
