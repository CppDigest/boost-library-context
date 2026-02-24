# #15 index_range.size() was missing [Closed]

> Username: CromwellEnage  
> Created at: 2018-09-19 00:22:34 UTC  
> Updated at: 2018-09-19 15:48:18 UTC  
> Closed at: 2018-09-19 15:28:44 UTC  
> Url: https://github.com/boostorg/multi_array/pull/15  

- Added missing size() member function to index_range in accordance with reference documentation.  
- Test program depends on PR #14.

---

## Comment 1

> Username: glenfe  
> Created_at: 2018-09-19 11:44:31 UTC  
> Url: https://github.com/boostorg/multi_array/pull/15#issuecomment-422769921  

This is still not right. There shouldn't be 20+ commits in this, just one that adds size().   
  
Furthermore, check the Changes link for this pull request: https://github.com/boostorg/multi_array/pull/15/files  
  
It shows *way* more changes than just adding a single size() function. (e.g. It shows my allocator changes, your Travis changes, etc. etc.) All your other pull requests have the same problem.  
  
You need to make sure the pull requests for a specific feature add only that feature. And the commits in the pull request reflect only that.

---

## Comment 2

> Username: CromwellEnage  
> Created_at: 2018-09-19 15:23:37 UTC  
> Url: https://github.com/boostorg/multi_array/pull/15#issuecomment-422845504  

Okay.  From what I know, I'll have to make a new repository in order to make some clean pull requests.  This will take a hot minute...

---

## Comment 3

> Username: glenfe  
> Created_at: 2018-09-19 15:48:18 UTC  
> Url: https://github.com/boostorg/multi_array/pull/15#issuecomment-422854647  

You can do it with the current repository. For example:  
  
> $ git checkout -b index_range_size  
> $ git reset --hard origin/develop  
> $ git pull https://github.com/boostorg/multi_array develop  
  
Now make your changes for the feature on this branch. Then:  
  
> $ git commit -m "Add missing index_range.size()"  
> $ git push origin index_range_size  
  
Then open a pull request from `CromwellEnage/multi_array:index_range_size` (i.e. the new branch on your end) to `boostorg/multi_array:develop` (i.e. the develop branch on our end).

---
