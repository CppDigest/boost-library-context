# #325 - Potential memory leak in file_collect_archive_content_ [Open]

> Username: jwakely  
> Created at: 2018-07-18 14:42:54 UTC  
> Updated at: 2021-06-26 03:10:06 UTC  
> Url: https://github.com/boostorg/build/issues/325  

https://github.com/boostorg/build/blob/0c6d016/src/engine/fileunix.c#L324 performs a memory allocation which has a chance of leaking when processing a corrupt archive.  
  
In the unlikely situation where an archive has two string tables (or simply one string table and another entry with a name that starts with `//`) there will be a second allocation and the original value of `string_table` will be leaked.  
  
This probably isn't worth fixing, I'm only reporting it because Coverity flags it as a leak. If you do want to make any change then it's probably enough to just do something like:  
  
    assert(!string_table);  
  
before the allocation.

---

## Comment 1

> Username: stale[bot]  
> Created at: 2021-06-26 03:09:24 UTC  
> Url: https://github.com/boostorg/build/issues/325#issuecomment-868936431  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.
