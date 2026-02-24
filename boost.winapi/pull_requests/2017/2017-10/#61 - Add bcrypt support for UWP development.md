# #61 Add bcrypt support for UWP development [Merged]

> Username: jeking3  
> Created at: 2017-10-07 16:33:52 UTC  
> Updated at: 2017-10-07 18:50:07 UTC  
> Merged at: 2017-10-07 18:14:06 UTC  
> Closed at: 2017-10-07 18:14:06 UTC  
> Url: https://github.com/boostorg/winapi/pull/61  

The older windows crypto methods are not available in some UWP partitions (like ``WINAPI_PARTITION_PHONE_APP``).  To support those, specifically in random number generation methods, bcrypt support is added.  
  
I have built against this using local changes in Boost.Random and it works for the phone partition.

---

## Comment 1

> Username: jeking3  
> Created_at: 2017-10-07 17:28:08 UTC  
> Url: https://github.com/boostorg/winapi/pull/61#issuecomment-334952231  

@Lastique do you want me to rebase this?  There are no conflicts so it should be fine, but I can rebase it to kick off a new set of tests.  Let me know.  This is the last thing holding me off from submitting Boost.Random changes.

---
