# #114 Add definition of file_descriptor_source::open() [Open]

> Username: here-mikelley  
> Created at: 2020-05-01 19:30:37 UTC  
> Updated at: 2020-05-01 22:26:25 UTC  
> Url: https://github.com/boostorg/iostreams/pull/114  

This commit adds the missing definition of the overload of  
file_descriptor_source::open() that accepts a boost::filesystem::path.

---

## Comment 1

> Username: mclow  
> Created_at: 2020-05-01 22:01:30 UTC  
> Url: https://github.com/boostorg/iostreams/pull/114#issuecomment-622585425  

Tests?

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2020-05-01 22:20:34 UTC  
> Updated_at: 2020-05-01 22:26:25 UTC  
> Url: https://github.com/boostorg/iostreams/pull/114#issuecomment-622590676  

# [Codecov](https://codecov.io/gh/boostorg/iostreams/pull/114?src=pr&el=h1) Report  
> Merging [#114](https://codecov.io/gh/boostorg/iostreams/pull/114?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/iostreams/commit/87d113c6a3075d4aeb44aebba38134dcac1e90c0&el=desc) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/iostreams/pull/114/graphs/tree.svg?width=650&height=150&src=pr&token=LBEfwtNfca)](https://codecov.io/gh/boostorg/iostreams/pull/114?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #114   +/-   ##  
========================================  
  Coverage    68.84%   68.84%             
========================================  
  Files           80       80             
  Lines         3444     3444             
  Branches      1027     1027             
========================================  
  Hits          2371     2371             
  Misses         454      454             
  Partials       619      619             
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/iostreams/pull/114?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/iostreams/device/file\_descriptor.hpp](https://codecov.io/gh/boostorg/iostreams/pull/114/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9pb3N0cmVhbXMvZGV2aWNlL2ZpbGVfZGVzY3JpcHRvci5ocHA=) | `100.00% <ø> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/iostreams/pull/114?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/iostreams/pull/114?src=pr&el=footer). Last update [87d113c...0c8f2fd](https://codecov.io/gh/boostorg/iostreams/pull/114?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---
