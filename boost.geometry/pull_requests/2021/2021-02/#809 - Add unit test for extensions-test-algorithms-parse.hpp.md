# #809 Add unit test for extensions/test/algorithms/parse.hpp [Closed]

> Username: ayushgupta138  
> Created at: 2021-02-15 10:37:03 UTC  
> Updated at: 2021-02-15 11:46:42 UTC  
> Closed at: 2021-02-15 11:45:01 UTC  
> Url: https://github.com/boostorg/geometry/pull/809  

Second patch for unit test for extensions/test/algorithms/parse.hpp ( #788 ).

---

## Comment 1

> Username: ayushgupta138  
> Created_at: 2021-02-15 10:53:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/809#issuecomment-779139004  

@mloskot  apologies, I could not push to the previous pr #806  due to some conflicts that i couldn't resolve. Please consider this patch for modifications in parse.cpp.

---

## Comment 2

> Username: mloskot  
> Created_at: 2021-02-15 11:00:12 UTC  
> Url: https://github.com/boostorg/geometry/pull/809#issuecomment-779142967  

As I mentioned in https://github.com/boostorg/geometry/pull/806#issuecomment-779049596 in the point 4 is that you need to force-push to your branch: `git push --force` and this will overwrite the old branch with the new branch.

---

## Comment 3

> Username: mloskot  
> Created_at: 2021-02-15 11:02:19 UTC  
> Url: https://github.com/boostorg/geometry/pull/809#issuecomment-779144335  

@ayushgupta138  By the way, it is a bad idea to submit any contributions from your fork's `develop` branch (like you did here). Instead, always create a topic branch and submit PR from a topic branch. That way, you can easily 'replace' PR branches in your remote without affecting your local develop branch. Topic branches save time!

---

## Comment 4

> Username: ayushgupta138  
> Created_at: 2021-02-15 11:45:01 UTC  
> Url: https://github.com/boostorg/geometry/pull/809#issuecomment-779168485  

Thanks for your advice. I will keep that in mind. I have submitted the changes as you said before on the previous pr #806 . Hence, I am closing this pr.

---
