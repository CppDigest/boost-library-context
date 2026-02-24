# #212 Feature/concurrent_flat_set [Merged]

> Username: joaquintides  
> Created at: 2023-09-11 11:21:38 UTC  
> Updated at: 2024-08-17 16:48:07 UTC  
> Merged at: 2023-09-16 16:36:37 UTC  
> Closed at: 2023-09-16 16:36:37 UTC  
> Url: https://github.com/boostorg/unordered/pull/212  



---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2023-09-11 11:32:11 UTC  
> Url: https://github.com/boostorg/unordered/pull/212#issuecomment-1713697434  

An automated preview of the documentation is available at [https://212.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html](https://212.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html)

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2023-09-11 19:02:55 UTC  
> Updated_at: 2023-09-11 19:11:32 UTC  
> Url: https://github.com/boostorg/unordered/pull/212#issuecomment-1714426097  

## [Codecov](https://app.codecov.io/gh/boostorg/unordered/pull/212?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#212](https://app.codecov.io/gh/boostorg/unordered/pull/212?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (2a55e26) into [develop](https://app.codecov.io/gh/boostorg/unordered/commit/215d8e82cb3de69ee60dca29b3531c588c560b91?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (215d8e8) will **increase** coverage by `0.02%`.  
> The diff coverage is `99.47%`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/unordered/pull/212/graphs/tree.svg?width=650&height=150&src=pr&token=ZqRPZlJZ5N&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/unordered/pull/212?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #212      +/-   ##  
===========================================  
+ Coverage    97.89%   97.92%   +0.02%       
===========================================  
  Files          138      140       +2       
  Lines        19471    19640     +169       
===========================================  
+ Hits         19062    19233     +171       
+ Misses         409      407       -2       
```  
  
  
| [Files Changed](https://app.codecov.io/gh/boostorg/unordered/pull/212?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [test/cfoa/serialization\_tests.cpp](https://app.codecov.io/gh/boostorg/unordered/pull/212?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC9jZm9hL3NlcmlhbGl6YXRpb25fdGVzdHMuY3Bw) | `100.00% <ø> (ø)` | |  
| [test/cfoa/try\_emplace\_tests.cpp](https://app.codecov.io/gh/boostorg/unordered/pull/212?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC9jZm9hL3RyeV9lbXBsYWNlX3Rlc3RzLmNwcA==) | `100.00% <ø> (ø)` | |  
| [test/cfoa/swap\_tests.cpp](https://app.codecov.io/gh/boostorg/unordered/pull/212?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC9jZm9hL3N3YXBfdGVzdHMuY3Bw) | `93.75% <80.95%> (+3.27%)` | :arrow_up: |  
| [test/cfoa/clear\_tests.cpp](https://app.codecov.io/gh/boostorg/unordered/pull/212?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC9jZm9hL2NsZWFyX3Rlc3RzLmNwcA==) | `98.27% <90.00%> (+0.06%)` | :arrow_up: |  
| [include/boost/unordered/concurrent\_flat\_map.hpp](https://app.codecov.io/gh/boostorg/unordered/pull/212?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91bm9yZGVyZWQvY29uY3VycmVudF9mbGF0X21hcC5ocHA=) | `100.00% <100.00%> (ø)` | |  
| [include/boost/unordered/concurrent\_flat\_set.hpp](https://app.codecov.io/gh/boostorg/unordered/pull/212?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91bm9yZGVyZWQvY29uY3VycmVudF9mbGF0X3NldC5ocHA=) | `100.00% <100.00%> (ø)` | |  
| [include/boost/unordered/unordered\_flat\_set.hpp](https://app.codecov.io/gh/boostorg/unordered/pull/212?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91bm9yZGVyZWQvdW5vcmRlcmVkX2ZsYXRfc2V0LmhwcA==) | `100.00% <100.00%> (ø)` | |  
| [test/cfoa/assign\_tests.cpp](https://app.codecov.io/gh/boostorg/unordered/pull/212?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC9jZm9hL2Fzc2lnbl90ZXN0cy5jcHA=) | `100.00% <100.00%> (ø)` | |  
| [test/cfoa/common\_helpers.hpp](https://app.codecov.io/gh/boostorg/unordered/pull/212?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC9jZm9hL2NvbW1vbl9oZWxwZXJzLmhwcA==) | `100.00% <100.00%> (ø)` | |  
| [test/cfoa/constructor\_tests.cpp](https://app.codecov.io/gh/boostorg/unordered/pull/212?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC9jZm9hL2NvbnN0cnVjdG9yX3Rlc3RzLmNwcA==) | `99.06% <100.00%> (+0.53%)` | :arrow_up: |  
| ... and [16 more](https://app.codecov.io/gh/boostorg/unordered/pull/212?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | |  
  
... and [1 file with indirect coverage changes](https://app.codecov.io/gh/boostorg/unordered/pull/212/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/unordered/pull/212?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/unordered/pull/212?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [215d8e8...2a55e26](https://app.codecov.io/gh/boostorg/unordered/pull/212?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Review 3 [Commented]

> Username: cmazakas  
> Created_at: 2023-09-15 19:26:30 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/unordered/pull/212#pullrequestreview-1629584312  

📁 doc/unordered/concurrent.adoc

```diff
 114 | 
 115 |- Visitation is prominent in the API provided by `boost::concurrent_flat_map`, and
 115 |+ Visitation is prominent in the API provided by `boost::concurrent_flat_ser` and `boost::concurrent_flat_map`, and
```

> Username: cmazakas  
> Created_at: 2023-09-15 19:26:30 UTC  
> Url: https://github.com/boostorg/unordered/pull/212#discussion_r1327696684  

Just noticed, this says `boost::concurrent_flat_ser` and not `set`.


---

## Review 4 [Commented]

> Username: cmazakas  
> Created_at: 2023-09-15 19:27:05 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/unordered/pull/212#pullrequestreview-1629584954  

📁 doc/unordered/concurrent.adoc

```diff
  48 |- not a https://en.cppreference.com/w/cpp/named_req/Container[Container^]
  46 |+ The first thing a new user of `boost::concurrent_flat_set` or `boost::concurrent_flat_map`
  47 |+ will notice is that these classes _do not provide iterators_ (which makes then technically
```

> Username: cmazakas  
> Created_at: 2023-09-15 19:27:05 UTC  
> Url: https://github.com/boostorg/unordered/pull/212#discussion_r1327697098  

`then` => `them`


---

## Comment 5

> Username: cppalliance-bot  
> Created_at: 2023-09-16 07:57:03 UTC  
> Url: https://github.com/boostorg/unordered/pull/212#issuecomment-1722169405  

An automated preview of the documentation is available at [https://212.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html](https://212.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html)

---

## Comment 6

> Username: cppalliance-bot  
> Created_at: 2023-09-16 08:27:06 UTC  
> Url: https://github.com/boostorg/unordered/pull/212#issuecomment-1722175545  

An automated preview of the documentation is available at [https://212.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html](https://212.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html)

---

## Comment 7

> Username: cppalliance-bot  
> Created_at: 2023-09-16 09:27:03 UTC  
> Url: https://github.com/boostorg/unordered/pull/212#issuecomment-1722187652  

An automated preview of the documentation is available at [https://212.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html](https://212.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html)

---

## Comment 8

> Username: cppalliance-bot  
> Created_at: 2023-09-16 10:27:03 UTC  
> Url: https://github.com/boostorg/unordered/pull/212#issuecomment-1722198473  

An automated preview of the documentation is available at [https://212.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html](https://212.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html)

---
