# #2 Cherry pick unmerged changes from develop [Closed]

> Username: danieljames  
> Created at: 2014-06-08 17:29:43 UTC  
> Updated at: 2015-07-19 17:13:13 UTC  
> Closed at: 2015-07-19 17:13:13 UTC  
> Url: https://github.com/boostorg/parameter/pull/2  

This pull request is against master, in order to update it with the latest code changes from develop. After this change the only remaining unmerged changes are documentation changes, which includes the literate tests extracted from the documentation. I left them out because I don't know why they were never merged to release in subversion.

---

## Comment 1

> Username: eldiener  
> Created_at: 2015-07-19 17:13:13 UTC  
> Url: https://github.com/boostorg/parameter/pull/2#issuecomment-122684302  

I manually cherry-picked the necessary fixes above on 'master'. I did not merge the literate tests from 'develop' to 'master', but that can be done as a separate task if necessary. I did fix the only literate test on 'develop' that had been failing due to ADL and pushed that to 'develop' so if we want to merge the literate tests to 'master' we should not have any test failures after we do.

---
