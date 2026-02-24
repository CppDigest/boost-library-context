# #57 Added rotate algorithm [Closed]

> Username: roshanrags  
> Created at: 2014-03-10 00:37:08 UTC  
> Updated at: 2014-03-10 14:30:24 UTC  
> Closed at: 2014-03-10 14:30:24 UTC  
> Url: https://github.com/boostorg/compute/pull/57  



---

## Comment 1

> Username: kylelutz  
> Created_at: 2014-03-10 01:09:45 UTC  
> Url: https://github.com/boostorg/compute/pull/57#issuecomment-37146529  

Thanks for working on this!  
  
I've left a few notes in the commit for some small fixes. Also, could you add a test case? A good example would be the `test/test_nth_element.cpp` file. I'd create a small vector with 10 int's and try rotating it by 1, 5, and 9 places and make sure each gives the correct result (also try rotating by 0 and 10 places and ensure the result is the same as the input). After that this should be good to merge!  
  
When making these changes use `git commit --amend` to update this commit with the fixes. Then push it again to github with `git push -f origin HEAD`. Usually I'd use a more specific branch name such as "rotate" rather than just "master" as this makes the merge in the git logs easier to understand. To do this just checkout a new branch named "rotate" and do the commit there. Also, change the pull request to target the "develop" branch. This is where I integrate and test new features before merging them into the stable "master" branch. You can change this with the edit button at the top of the pull-request page.  
  
Thanks again, and let me know if you run into any issues!

---

## Comment 2

> Username: roshanrags  
> Created_at: 2014-03-10 14:30:24 UTC  
> Url: https://github.com/boostorg/compute/pull/57#issuecomment-37187687  

Submitting new pull request

---
