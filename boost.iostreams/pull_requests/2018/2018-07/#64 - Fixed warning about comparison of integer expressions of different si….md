# #64 Fixed warning about comparison of integer expressions of different si… [Merged]

> Username: kivadiu  
> Created at: 2018-07-30 17:03:27 UTC  
> Updated at: 2018-08-01 13:05:00 UTC  
> Merged at: 2018-08-01 13:04:59 UTC  
> Closed at: 2018-08-01 13:05:00 UTC  
> Url: https://github.com/boostorg/iostreams/pull/64  

…gnedness  
  
size_ is a stream_offset which is a boost::intmax_t which is signed  
max_length is a mapped_file_source::size_type which is a std::size_t  
which is unsigned  
  
It is not expected that size_ be negative here so we cast it to the  
unsigned integer.  
  
Fixes #63

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2018-07-30 19:15:49 UTC  
> Updated_at: 2018-07-31 08:29:05 UTC  
> Url: https://github.com/boostorg/iostreams/pull/64#issuecomment-408978337  

# [Codecov](https://codecov.io/gh/boostorg/iostreams/pull/64?src=pr&el=h1) Report  
> Merging [#64](https://codecov.io/gh/boostorg/iostreams/pull/64?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/iostreams/commit/d7f1b826825d5e143e0b2d36dc7ba12d5210b19d?src=pr&el=desc) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/iostreams/pull/64/graphs/tree.svg?width=650&height=150&src=pr&token=LBEfwtNfca)](https://codecov.io/gh/boostorg/iostreams/pull/64?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop      #64   +/-   ##  
========================================  
  Coverage    64.71%   64.71%             
========================================  
  Files           81       81             
  Lines         3545     3545             
  Branches       950      950             
========================================  
  Hits          2294     2294             
  Misses         461      461             
  Partials       790      790  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/iostreams/pull/64?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [src/mapped\_file.cpp](https://codecov.io/gh/boostorg/iostreams/pull/64/diff?src=pr&el=tree#diff-c3JjL21hcHBlZF9maWxlLmNwcA==) | `49.33% <ø> (ø)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/iostreams/pull/64?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/iostreams/pull/64?src=pr&el=footer). Last update [d7f1b82...3fa38ff](https://codecov.io/gh/boostorg/iostreams/pull/64?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Review 2 [Changes requested]

> Username: jeking3  
> Created_at: 2018-07-30 21:56:47 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/iostreams/pull/64#pullrequestreview-141732915  

📁 src/mapped_file.cpp

```diff
 325 |             (DWORD) (p.offset & 0xffffffff),
 326 |-             (SIZE_T) (size_ != max_length ? size_ : 0), 
 326 |+             (SIZE_T) (static_cast<size_type>(size_) != max_length ? size_ : 0),
```

> Username: jeking3  
> Created_at: 2018-07-30 21:56:44 UTC  
> Updated_at: 2018-07-31 05:20:55 UTC  
> Url: https://github.com/boostorg/iostreams/pull/64#discussion_r206335531  

I'd suggest using `boost::numeric_cast<>` so that in the event there is actually an overflow it will be handled correctly with an exception.  Otherwise looks good.


---

## Comment 3

> Username: kivadiu  
> Created_at: 2018-07-31 16:34:59 UTC  
> Url: https://github.com/boostorg/iostreams/pull/64#issuecomment-409285375  

So, the new PR passed CI tests. Is it acceptable now?

---

## Comment 4

> Username: jeking3  
> Created_at: 2018-08-01 13:04:26 UTC  
> Url: https://github.com/boostorg/iostreams/pull/64#issuecomment-409567334  

Yes, there are more warnings if you want to tackle them too.  
  
https://ci.appveyor.com/project/eldiener/iostreams/build/1.0.53-develop/job/cnh2bfu25rj2yorg

---
