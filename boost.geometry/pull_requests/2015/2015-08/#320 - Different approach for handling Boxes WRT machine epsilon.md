# #320 Different approach for handling Boxes WRT machine epsilon [Merged]

> Username: awulkiew  
> Created at: 2015-08-21 18:21:30 UTC  
> Updated at: 2015-08-28 12:27:38 UTC  
> Merged at: 2015-08-25 14:40:55 UTC  
> Closed at: 2015-08-25 14:40:55 UTC  
> Url: https://github.com/boostorg/geometry/pull/320  

This PR reverts the changes done by PR https://github.com/boostorg/geometry/pull/318 and addresses the `sectionalize`/`partition` problem in a different way.  
  
Spatial relations between Boxes and other Geometries must be checked as fast as possible (see the other PR for more info). This is why raw comparison operators gives the advantage here. Other Geometries are spatially-compared taking machine epsilon into account, so using functions like `math::equals()`. This is clearly an inconsistency, but I propose to keep it. Instead of making the checks consistent this PR enlarges the Boxes WRT epsilon in `sectionalize()` in a way that should be consistent with `math::equal()` checks performed for other Geometries.  
  
I modified and used a function that was already there: `enlarge_sections()`. It wasn't used but in there (2D) sections was enlarged by `relaxed_epsilon(10)`. This could work in some cases but I think not in all of them because in `math::equals()` the epsilon is scaled WRT the greatest absolute value by default. I saw a comment there which mentions some robustness issues related to one of the union tests. The new approach doesn't break this test. What was the exact problem related to this code?

---

## Comment 1

> Username: mkaravel  
> Created_at: 2015-08-22 23:31:20 UTC  
> Url: https://github.com/boostorg/geometry/pull/320#issuecomment-133764262  

Looks good to me. I am in favor of merging.

---

## Comment 2

> Username: awulkiew  
> Created_at: 2015-08-25 14:40:44 UTC  
> Url: https://github.com/boostorg/geometry/pull/320#issuecomment-134608771  

Ok thanks!  
There are no failures with robustness enabled. With robustness disabled there is the same number of failures as before the PR https://github.com/boostorg/geometry/pull/318. The interesting thing is that it's one failure less WRT the current develop so after merging the mentioned PR. I'll write about it on the corresponding PR's page. Therefore I'm going to merge it. Barend and the others may comment afterwards.

---

## Comment 3

> Username: barendgehrels  
> Created_at: 2015-08-28 07:31:31 UTC  
> Url: https://github.com/boostorg/geometry/pull/320#issuecomment-135662034  

It is slower than the previous (reverted) approach. This approach is (for buffer) around 38% slower.  
Most probably because much more boxes overlap now - leading into more operations.

---

## Comment 4

> Username: barendgehrels  
> Created_at: 2015-08-28 07:33:39 UTC  
> Url: https://github.com/boostorg/geometry/pull/320#issuecomment-135662313  

For next time, please do this in two steps. First revert the changes from a previous commit or PR. That basically does not have to be reviewed.  
Then do the new action.  
  
Because this is much more time consuming to review... The change itself is probably quite small.

---

## Comment 5

> Username: barendgehrels  
> Created_at: 2015-08-28 08:11:40 UTC  
> Url: https://github.com/boostorg/geometry/pull/320#issuecomment-135667693  

Performance for union seems completely unaffected

---

## Comment 6

> Username: awulkiew  
> Created_at: 2015-08-28 12:27:38 UTC  
> Url: https://github.com/boostorg/geometry/pull/320#issuecomment-135761950  

Got it, next time I'll revert in one commit. However then some of the tests would be failing. Should I then also revert them or only disable them?  
  
Yes, I'd be surprised if the performance was harmed directly by this change. But it's possible that sectionalize could be made faster if boxes were enlarged for a section right away, so not at the end of the algorithm for all sections becasue then there is a lot of cold data around when sections are iterated. Not to mention that there is in general a lot of cold data in a section code. If we had an index-like structure (containing only std::pair<Box, ID>) and sections in some other container I bet the partition would be faster several times.  
  
Regarding the buffer, there seems to be something wrong with handling of Boxes by this algorithm (detected failing cases for the previously used approach and rescaling disabled). But to know for sure we should detect the failing cases or not performing very well and see what's the exact reason. In the case of which geometry there is a performance regression? Or is it there for all of the geometries?

---
