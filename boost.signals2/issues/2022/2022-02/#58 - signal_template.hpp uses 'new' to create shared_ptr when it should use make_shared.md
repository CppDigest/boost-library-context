# #58 - signal_template.hpp uses 'new' to create shared_ptr when it should use make_shared [Closed]

> Username: BrianWeed  
> Created at: 2022-02-15 22:33:33 UTC  
> Updated at: 2022-05-25 15:16:35 UTC  
> Closed at: 2022-05-25 15:16:35 UTC  
> Url: https://github.com/boostorg/signals2/issues/58  

boost_1_78_0\boost\signals2\detail\signal_template.hpp  lines 156, 309, 478, 500, and 513 use 'new' when make_shared would be more efficient. ( There's probably even more places in the code base that could use make_shared, but lines 156, and 513 were the ones flagged by the profiler, and are unnecessary bottlenecks).  
  
Calling new to create a shared pointer forces an additional allocation for the shared count (new sp_counted_impl_p).  
Using make_shared, on the other hand, combines the allocations into one, thereby cutting the total number of allocations/deallocations in half.

---

## Comment 1

> Username: fmhess  
> Created at: 2022-02-15 22:47:23 UTC  
> Url: https://github.com/boostorg/signals2/issues/58#issuecomment-1040874497  

Using make_shared sounds fine, would you make a pull request to the develop branch with the changes?  If not, let me know and I'll make the changes later.

---

## Comment 2

> Username: BrianWeed  
> Created at: 2022-02-15 22:57:02 UTC  
> Url: https://github.com/boostorg/signals2/issues/58#issuecomment-1040880348  

I’m not really setup to contribute changes right now. It would be ideal if you could do it.  
  
Thanks.  
  
Sent from my iPad  
  
> On Feb 15, 2022, at 5:47 PM, Frank Mori Hess ***@***.***> wrote:  
>   
> ﻿  
> Using make_shared sounds fine, would you make a pull request to the develop branch with the changes? If not, let me know and I'll make the changes later.  
>   
> —  
> Reply to this email directly, view it on GitHub, or unsubscribe.  
> Triage notifications on the go with GitHub Mobile for iOS or Android.   
> You are receiving this because you authored the thread.

---

## Comment 3

> Username: fmhess  
> Created at: 2022-05-25 15:16:35 UTC  
> Url: https://github.com/boostorg/signals2/issues/58#issuecomment-1137424909  

These changes have been merged to master, so should be in boost 1.80
