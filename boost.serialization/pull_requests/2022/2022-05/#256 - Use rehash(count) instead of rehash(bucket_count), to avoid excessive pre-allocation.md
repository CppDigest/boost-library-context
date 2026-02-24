# #256 Use rehash(count) instead of rehash(bucket_count), to avoid excessive pre-allocation. [Merged]

> Username: BrianWeed  
> Created at: 2022-05-04 19:21:11 UTC  
> Updated at: 2022-09-15 21:08:31 UTC  
> Merged at: 2022-09-15 21:08:31 UTC  
> Closed at: 2022-09-15 21:08:31 UTC  
> Url: https://github.com/boostorg/serialization/pull/256  

On windows, the number of buckets in an unordered_map never decreases: https://godbolt.org/z/9jrMv6Gah  
When an archive is saved, the bucket_count is the MAX that it ever was in that session, no matter what count is.  
When that archive is loaded, even though count may be small, the rehash would allocate way more memory than is necessary.  
  
This change solves that by using count for the rehash, which will pre-allocate the appropriate number of buckets, on each platform: https://godbolt.org/z/6fjfWGh47

---

## Comment 1

> Username: robertramey  
> Created_at: 2022-05-05 00:34:15 UTC  
> Url: https://github.com/boostorg/serialization/pull/256#issuecomment-1118058627  

question: if one loads a previously existing archive with the old count type, will it get automatically converted to the new count type?

---

## Comment 2

> Username: BrianWeed  
> Created_at: 2022-05-05 14:15:51 UTC  
> Url: https://github.com/boostorg/serialization/pull/256#issuecomment-1118609242  

I don't fully understand your question. count and bucket_count are both the same type: collection_size_type  
If you are asking if bucket_count will be updated (upon save) to a new more meaningful value, Yes:  
  
ExistingArchive:  
<count>2</count>  
<bucket_count>17961079</bucket_count>  
  
Load ExistingArchive  
no inserts this session  
Save NewArchive  
  
NewArchive:  
<count>2</count>  
<bucket_count>8</bucket_count>  
  
This fixes issue #255

---
