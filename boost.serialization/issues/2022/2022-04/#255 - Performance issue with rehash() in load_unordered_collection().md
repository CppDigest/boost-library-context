# #255 - Performance issue with rehash() in load_unordered_collection() [Closed]

> Username: BrianWeed  
> Created at: 2022-04-27 20:31:17 UTC  
> Updated at: 2022-09-16 23:18:50 UTC  
> Closed at: 2022-09-16 23:18:39 UTC  
> Url: https://github.com/boostorg/serialization/issues/255  

https://github.com/boostorg/serialization/blob/0ca603daf99888bf059c01ae1bab1b27dbc35ebe/include/boost/serialization/unordered_collections_load_imp.hpp#L62  
  
On Windows, std::unordered_map will never shrink the number of buckets it uses, so when we save a map, the bucket_count is always the MAX that it ever was in that session (no matter how many actual entries are in the map at save-time).  Loading this via load_unordered_collection() will restore that max bucket_count, even when there are only 2 items in the map.   
For example, we have one archive with multiple entries like this:  
  
<count>2</count>  
<bucket_count>17961079</bucket_count>  
  
Calling rehash(bucket_count) for this costs 50GB, for a map with only 2 items.  
  
I recommend using count, instead of bucket_count, as in:  
s.rehash(count);  
  
That should still pre-allocate an appropriately sized buffer (for performance purposes), while also avoiding the problem when the saved bucket_count no longer makes sense given the actual count.

---

## Comment 1

> Username: robertramey  
> Created at: 2022-04-27 21:15:03 UTC  
> Url: https://github.com/boostorg/serialization/issues/255#issuecomment-1111490668  

looks like you spent serious time in figuring all this out.  So I'm going to trust your judgement.  convert this to a PR, make your updates, retest, and submit the PR.  Try to re-run the whole test suite.  I do this because it's too easy to accidentally break something that seemed totally unrelated but turned out to be unconnected in a subtle way.  Also doing it this way guarantees the you get the credit (or blame!) for fixing this.  Finally, while you're at it take a look a the wider code.  I believe that there at least implementations of unordered map the std version and a boost version.  It's also possible that other collections types have similar or related issues no one has detected.    
  
I'm know I'm asking for more than the minimum but I don't want to waste the opportunity to improve things by someone who has made the effort to understand all the issues.  
  
Again, many thanks for this.

---

## Comment 2

> Username: BrianWeed  
> Created at: 2022-05-04 19:27:33 UTC  
> Updated at: 2022-05-04 19:30:11 UTC  
> Url: https://github.com/boostorg/serialization/issues/255#issuecomment-1117739049  

I created a PR, but don't see how to link it to this issue.  (Today is my first time using git and GitHub.)  
  
I did build and test this change on Linux before submitting.  
  
I have not yet looked for more instances of this issue. I thought I should get this one correct first.

---

## Comment 3

> Username: robertramey  
> Created at: 2022-05-05 00:32:30 UTC  
> Url: https://github.com/boostorg/serialization/issues/255#issuecomment-1118058002  

I don't think there is an explicit way to link between the two.  Just submit the PR and in the comment say that it fixes issue number x.

---

## Comment 4

> Username: robertramey  
> Created at: 2022-09-16 23:18:49 UTC  
> Url: https://github.com/boostorg/serialization/issues/255#issuecomment-1249930922  

merged PR
