# #537 move abstract-file-target.root to virtual-target [Merged]

> Username: jehelset  
> Created at: 2020-02-09 13:28:53 UTC  
> Updated at: 2021-10-02 21:13:31 UTC  
> Merged at: 2020-02-09 14:45:45 UTC  
> Closed at: 2020-02-09 14:45:45 UTC  
> Url: https://github.com/boostorg/build/pull/537  

seems to only be called from basic-target.create-subvariant, which  
seems (looking at the comments and 5.6.4 of docs) to assume that it  
exists on virtual-target.  
  
without this fix a user wanting to produce something derived from  
virtual-target in the construct-function of something derived from  
basic-target will get an error.

---
