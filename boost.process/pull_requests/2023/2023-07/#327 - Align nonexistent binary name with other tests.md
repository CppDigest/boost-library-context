# #327 Align nonexistent binary name with other tests [Closed]

> Username: SilverPlate3  
> Created at: 2023-07-02 19:48:54 UTC  
> Updated at: 2023-08-15 03:49:08 UTC  
> Closed at: 2023-08-15 03:49:08 UTC  
> Url: https://github.com/boostorg/process/pull/327  

All the other tests that try to run a nonexistent binary use the name "doesnt-exist", which is a much more logical name to this purpose then "doesnt-exit".  
For example:  
https://github.com/boostorg/process/blob/bfb1ebb5bd24837b6292421dba3790f5701883ba/test/posix_specific.cpp#L96

---

## Comment 1

> Username: klemens-morgenstern  
> Created_at: 2023-07-03 02:37:46 UTC  
> Url: https://github.com/boostorg/process/pull/327#issuecomment-1617133259  

Lol, yeah you're right. To be fair, if it doesn't exist it also doesn't exit.

---

## Comment 2

> Username: SilverPlate3  
> Created_at: 2023-07-08 10:23:06 UTC  
> Url: https://github.com/boostorg/process/pull/327#issuecomment-1627070269  

@klemens-morgenstern   
When will this fix be integrated?   
I think it will be nice if merged before next Boost release.

---

## Comment 3

> Username: klemens-morgenstern  
> Created_at: 2023-08-14 09:51:56 UTC  
> Url: https://github.com/boostorg/process/pull/327#issuecomment-1677024315  

I don't think how this fixes anything, it's a name change of a nonexisting binary.

---

## Comment 4

> Username: SilverPlate3  
> Created_at: 2023-08-14 19:36:16 UTC  
> Url: https://github.com/boostorg/process/pull/327#issuecomment-1677946764  

@klemens-morgenstern   
Indeed it doesn't fix any behavior, but it aligns naming with the rest of the code + gives a more "correct" name.  
Clean code requires naming guide lines

---
