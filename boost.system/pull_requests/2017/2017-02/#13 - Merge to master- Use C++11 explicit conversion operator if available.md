# #13 Merge to master: Use C++11 explicit conversion operator if available [Closed]

> Username: Lastique  
> Created at: 2017-02-17 12:55:34 UTC  
> Updated at: 2017-09-13 12:47:46 UTC  
> Closed at: 2017-09-13 12:47:46 UTC  
> Url: https://github.com/boostorg/system/pull/13  

This change brings leftover parts of commit  
08ea984237321c015a03705612eca910a96643f8 to master, in order to synchronize it  
with develop.  
  
For some reason, this part did not get merged to master by https://github.com/boostorg/system/pull/11. After this change, develop and master should have equivalent contents.

---

## Comment 1

> Username: Lastique  
> Created_at: 2017-09-13 12:47:46 UTC  
> Url: https://github.com/boostorg/system/pull/13#issuecomment-329156558  

This PR seems no longer actual. I do not see the changes in the current develop or master, the support for explicit operator `bool` seem to have been removed. Explicit conversion operators might be a good idea, but this PR is not the way to introduce them.

---
