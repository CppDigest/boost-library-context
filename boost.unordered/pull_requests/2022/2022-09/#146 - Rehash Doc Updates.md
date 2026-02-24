# #146 Rehash Doc Updates [Merged]

> Username: cmazakas  
> Created at: 2022-09-14 22:22:51 UTC  
> Updated at: 2022-09-15 17:49:20 UTC  
> Merged at: 2022-09-15 17:33:54 UTC  
> Closed at: 2022-09-15 17:33:54 UTC  
> Url: https://github.com/boostorg/unordered/pull/146  

Update the rehash() and reserve() reference docs for recently implemented behavior in #144

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2022-09-14 22:30:57 UTC  
> Url: https://github.com/boostorg/unordered/pull/146#issuecomment-1247366596  

An automated preview of the documentation is available at [https://146.unordered.prtest.cppalliance.org/libs/unordered/doc/html/unordered.html](https://146.unordered.prtest.cppalliance.org/libs/unordered/doc/html/unordered.html)

---

## Review 2 [Commented]

> Username: pdimov  
> Created_at: 2022-09-14 22:37:09 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/unordered/pull/146#pullrequestreview-1108290105  

📁 doc/unordered/unordered_map.adoc

```diff
1568 | 
1569 |- Changes the number of buckets so that there at least `n` buckets, and so that the load factor is less than the maximum load factor.
1569 |+ Changes the number of buckets so that there at least `n` buckets, and so that the load factor is less than or equal to the maximum load factor. When applicable, this will either grow or shrink the `bucket_count()` associated with the container.
```

> Username: pdimov  
> Created_at: 2022-09-14 22:37:09 UTC  
> Url: https://github.com/boostorg/unordered/pull/146#discussion_r971353896  

"are" is missing after "there".


---

## Review 3 [Commented]

> Username: pdimov  
> Created_at: 2022-09-14 22:38:50 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/unordered/pull/146#pullrequestreview-1108291429  

📁 doc/unordered/unordered_multimap.adoc

```diff
1347 | 
1348 |- Changes the number of buckets so that there at least `n` buckets, and so that the load factor is less than the maximum load factor.
1348 |+ Changes the number of buckets so that there at least `n` buckets, and so that the load factor is less than or equal to the maximum load factor. When applicable, this will either grow or shrink the `bucket_count()` associated with the container.
```

> Username: pdimov  
> Created_at: 2022-09-14 22:38:50 UTC  
> Url: https://github.com/boostorg/unordered/pull/146#discussion_r971355036  

Same as above, "are" missing after "there".


---

## Review 4 [Commented]

> Username: pdimov  
> Created_at: 2022-09-14 22:39:02 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/unordered/pull/146#pullrequestreview-1108291533  

📁 doc/unordered/unordered_multiset.adoc

```diff
1308 | 
1309 |- Changes the number of buckets so that there at least `n` buckets, and so that the load factor is less than the maximum load factor.
1309 |+ Changes the number of buckets so that there at least `n` buckets, and so that the load factor is less than or equal to the maximum load factor. When applicable, this will either grow or shrink the `bucket_count()` associated with the container.
```

> Username: pdimov  
> Created_at: 2022-09-14 22:39:02 UTC  
> Url: https://github.com/boostorg/unordered/pull/146#discussion_r971355127  

Same as above, "are" missing after "there".


---

## Review 5 [Commented]

> Username: pdimov  
> Created_at: 2022-09-14 22:39:11 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/unordered/pull/146#pullrequestreview-1108291619  

📁 doc/unordered/unordered_set.adoc

```diff
1331 | 
1332 |- Changes the number of buckets so that there at least `n` buckets, and so that the load factor is less than the maximum load factor.
1332 |+ Changes the number of buckets so that there at least `n` buckets, and so that the load factor is less than or equal to the maximum load factor. When applicable, this will either grow or shrink the `bucket_count()` associated with the container.
```

> Username: pdimov  
> Created_at: 2022-09-14 22:39:11 UTC  
> Url: https://github.com/boostorg/unordered/pull/146#discussion_r971355190  

Same as above, "are" missing after "there".


---

## Comment 6

> Username: codecov[bot]  
> Created_at: 2022-09-15 00:10:05 UTC  
> Updated_at: 2022-09-15 16:57:56 UTC  
> Url: https://github.com/boostorg/unordered/pull/146#issuecomment-1247420914  

# [Codecov](https://codecov.io/gh/boostorg/unordered/pull/146?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#146](https://codecov.io/gh/boostorg/unordered/pull/146?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (5dcccfd) into [develop](https://codecov.io/gh/boostorg/unordered/commit/6ef6540378783d4e57dd292c78c76b5bbee2d676?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (6ef6540) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/unordered/pull/146/graphs/tree.svg?width=650&height=150&src=pr&token=ZqRPZlJZ5N&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/unordered/pull/146?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #146   +/-   ##  
========================================  
  Coverage    97.49%   97.49%             
========================================  
  Files           75       75             
  Lines        10540    10540             
========================================  
  Hits         10276    10276             
  Misses         264      264             
```  
  
  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/unordered/pull/146?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/unordered/pull/146?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [6ef6540...5dcccfd](https://codecov.io/gh/boostorg/unordered/pull/146?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 7

> Username: cppalliance-bot  
> Created_at: 2022-09-15 15:06:04 UTC  
> Url: https://github.com/boostorg/unordered/pull/146#issuecomment-1248233700  

An automated preview of the documentation is available at [https://146.unordered.prtest.cppalliance.org/libs/unordered/doc/html/unordered.html](https://146.unordered.prtest.cppalliance.org/libs/unordered/doc/html/unordered.html)

---

## Review 8 [Approved]

> Username: joaquintides  
> Created_at: 2022-09-15 15:22:19 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/unordered/pull/146#pullrequestreview-1109400413  

No further comments

---
