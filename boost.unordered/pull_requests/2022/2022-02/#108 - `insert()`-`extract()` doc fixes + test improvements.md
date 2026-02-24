# #108 `insert()`/`extract()` doc fixes + test improvements [Merged]

> Username: cmazakas  
> Created at: 2022-02-25 22:37:00 UTC  
> Updated at: 2022-02-28 15:37:17 UTC  
> Merged at: 2022-02-26 02:01:30 UTC  
> Closed at: 2022-02-26 02:01:30 UTC  
> Url: https://github.com/boostorg/unordered/pull/108  

The documentation notes that inserting an extracted node from a map to a multimap isn't supported when in all reality, it's supported.  
  
We update the test dealing with `node_handle`s to prove this feature works and then update the corresponding documentation.

---
