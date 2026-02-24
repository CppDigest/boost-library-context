# #182 Feature/unordered node map docs [Merged]

> Username: joaquintides  
> Created at: 2023-02-13 12:31:15 UTC  
> Updated at: 2024-08-17 16:49:22 UTC  
> Merged at: 2023-02-25 09:20:05 UTC  
> Closed at: 2023-02-25 09:20:05 UTC  
> Url: https://github.com/boostorg/unordered/pull/182  

This is complete except the benchmarks, which I'll extend to cover `unordered_node_map` in the following days.

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2023-02-13 12:41:08 UTC  
> Url: https://github.com/boostorg/unordered/pull/182#issuecomment-1427876736  

An automated preview of the documentation is available at [https://182.unordered.prtest.cppalliance.org/libs/unordered/doc/html/unordered.html](https://182.unordered.prtest.cppalliance.org/libs/unordered/doc/html/unordered.html)

---

## Review 2 [Commented]

> Username: pdimov  
> Created_at: 2023-02-13 14:16:19 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/unordered/pull/182#pullrequestreview-1295731651  

📁 doc/unordered/compliance.adoc

```diff
 143 |+   ** `value_type` must be move-constructible.
 144 |+   ** Pointer stability is not kept under rehashing.
 145 |+   ** There is no API for node extraction/insertion
```

> Username: pdimov  
> Created_at: 2023-02-13 14:16:19 UTC  
> Url: https://github.com/boostorg/unordered/pull/182#discussion_r1104529824  

A period seems to be missing here.


---

## Comment 3

> Username: codecov[bot]  
> Created_at: 2023-02-13 17:20:29 UTC  
> Updated_at: 2023-02-25 09:03:28 UTC  
> Url: https://github.com/boostorg/unordered/pull/182#issuecomment-1428342259  

# [Codecov](https://codecov.io/gh/boostorg/unordered/pull/182?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#182](https://codecov.io/gh/boostorg/unordered/pull/182?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (d51cee3) into [develop](https://codecov.io/gh/boostorg/unordered/commit/0597463902a4d1ea85045e754facc3f1318ce269?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (0597463) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
> :exclamation: Current head d51cee3 differs from pull request most recent head c189d8d. Consider uploading reports for the commit c189d8d to get more accurate results  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/unordered/pull/182/graphs/tree.svg?width=650&height=150&src=pr&token=ZqRPZlJZ5N&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/unordered/pull/182?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #182   +/-   ##  
========================================  
  Coverage    97.72%   97.72%             
========================================  
  Files           85       85             
  Lines        12720    12720             
========================================  
  Hits         12431    12431             
  Misses         289      289             
```  
  
  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/unordered/pull/182?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/unordered/pull/182?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [0597463...c189d8d](https://codecov.io/gh/boostorg/unordered/pull/182?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 4

> Username: cppalliance-bot  
> Created_at: 2023-02-13 17:51:16 UTC  
> Url: https://github.com/boostorg/unordered/pull/182#issuecomment-1428396809  

An automated preview of the documentation is available at [https://182.unordered.prtest.cppalliance.org/libs/unordered/doc/html/unordered.html](https://182.unordered.prtest.cppalliance.org/libs/unordered/doc/html/unordered.html)

---

## Review 5 [Commented]

> Username: cmazakas  
> Created_at: 2023-02-13 20:04:27 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/unordered/pull/182#pullrequestreview-1296361140  

📁 doc/unordered/buckets.adoc

```diff
 263 |- As with all open-addressing containers, elements are stored directly in the bucket array.
 265 |+ As with all open-addressing containers, elements (or element nodes in the case of
 266 |+ `boost::unordered_node_map` and `boost::unordered_node_set`) are stored directly in the bucket array.
```

> Username: cmazakas  
> Created_at: 2023-02-13 20:04:26 UTC  
> Updated_at: 2023-02-13 20:04:27 UTC  
> Url: https://github.com/boostorg/unordered/pull/182#discussion_r1104962936  

Not sure I like the term" element nodes".  
  
What about something more like this:  
> As with all open-addressing containers, elements (or pointers thereto) are stored directly in the bucket array.

> Username: joaquintides  
> Created_at: 2023-02-18 10:54:02 UTC  
> Url: https://github.com/boostorg/unordered/pull/182#discussion_r1110981332  

Changed to "pointers to the element nodes": I think it's important that we retain the term "node" as we're constantly referring to "node-based containers".


---

## Review 6 [Commented]

> Username: cmazakas  
> Created_at: 2023-02-13 20:16:00 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/unordered/pull/182#pullrequestreview-1296376209  

📁 doc/unordered/compliance.adoc

```diff
 119 | 
 120 |- == Open-addressing containers: unordered_flat_set, unordered_flat_map
 120 |+ == Open-addressing containers: unordered_flat_set/unordered_node_set, unordered_flat_map/unordered_node_map
```

> Username: cmazakas  
> Created_at: 2023-02-13 20:15:59 UTC  
> Updated_at: 2023-02-13 20:16:00 UTC  
> Url: https://github.com/boostorg/unordered/pull/182#discussion_r1104973090  

These titles are getting a little unruly for me  
![Screenshot_20230213_121328](https://user-images.githubusercontent.com/5543573/218565213-f2497fad-dd74-47ae-9780-4f24cf6806df.png)  
  
It spills over in my browser and requires scrolling to read the entire thing.  
  
Instead, maybe we just trim this down to `Open-Addressing Containers` or something like that.

> Username: joaquintides  
> Created_at: 2023-02-18 11:06:14 UTC  
> Url: https://github.com/boostorg/unordered/pull/182#discussion_r1110982781  

Done.


---

## Comment 7

> Username: cppalliance-bot  
> Created_at: 2023-02-18 11:40:59 UTC  
> Url: https://github.com/boostorg/unordered/pull/182#issuecomment-1435649781  

An automated preview of the documentation is available at [https://182.unordered.prtest.cppalliance.org/libs/unordered/doc/html/unordered.html](https://182.unordered.prtest.cppalliance.org/libs/unordered/doc/html/unordered.html)

---

## Review 8 [Commented]

> Username: cmazakas  
> Created_at: 2023-02-22 17:34:26 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/unordered/pull/182#pullrequestreview-1309794122  

📁 doc/unordered/benchmarks.adoc

```diff
 285 | * `https://abseil.io/docs/cpp/guides/container[absl::flat_hash_map^]<uint64_t, uint64_t>`
 286 |- * `boost::unordered_flat_map<uint64_t, uint64_t>`
 286 | * `boost::unordered_map<uint64_t, uint64_t>`
```

> Username: cmazakas  
> Created_at: 2023-02-22 17:34:25 UTC  
> Updated_at: 2023-02-22 17:34:26 UTC  
> Url: https://github.com/boostorg/unordered/pull/182#discussion_r1114701344  

Should we replace `boost::unordered_map` with `absl::node_hash_map` for our benchmarks here?


---

## Comment 9

> Username: cppalliance-bot  
> Created_at: 2023-02-25 09:11:14 UTC  
> Url: https://github.com/boostorg/unordered/pull/182#issuecomment-1445037195  

An automated preview of the documentation is available at [https://182.unordered.prtest.cppalliance.org/libs/unordered/doc/html/unordered.html](https://182.unordered.prtest.cppalliance.org/libs/unordered/doc/html/unordered.html)

---
