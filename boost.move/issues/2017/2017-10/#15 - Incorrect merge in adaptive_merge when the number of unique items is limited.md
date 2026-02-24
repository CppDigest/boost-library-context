# #15 - Incorrect merge in adaptive_merge when the number of unique items is limited [Closed]

> Username: igaztanaga  
> Created at: 2017-10-07 12:29:02 UTC  
> Updated at: 2017-10-07 15:00:27 UTC  
> Closed at: 2017-10-07 12:36:15 UTC  
> Url: https://github.com/boostorg/move/issues/15  

Currently when the number of unique elements of the left sequence is limited:  
  
      //Not the minimum number of keys is not available on the first range, so fallback to rotations  
      if(collected != to_collect && collected < 4){  
         merge_bufferless(first, first + len1, first + len1 + len2, comp);  
      }  
  
but this logic is invalid as the first sequence has been mutated when extracting elements. Instead, the extracted elements must be merged back to the sequence before merging with the second sequence:  
  
      //Not the minimum number of keys is not available on the first range, so fallback to rotations  
      if(collected != to_collect && collected < 4){  
         merge_bufferless(first, first+collected, first+len1, comp);  
         merge_bufferless(first, first + len1, first + len1 + len2, comp);  
         return;  
      }

---

## Comment 1

> Username: igaztanaga  
> Created at: 2017-10-07 12:36:15 UTC  
> Url: https://github.com/boostorg/move/issues/15#issuecomment-334932160  

Fixed in commit:  
  
https://github.com/boostorg/move/commit/fcf217b8ec4e3817b1f9f7ec637fd590e089c358

---

## Comment 2

> Username: vinniefalco  
> Created at: 2017-10-07 15:00:27 UTC  
> Url: https://github.com/boostorg/move/issues/15#issuecomment-334941024  

Well, I'm glad I tried to diagnose it but to be honest, I would have never figured this out :)
