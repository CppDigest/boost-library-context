# #256 added an application for detecting horizontal and vertical edges in a… [Closed]

> Username: sid19991  
> Created at: 2019-03-09 05:04:33 UTC  
> Updated at: 2019-03-14 19:14:38 UTC  
> Closed at: 2019-03-14 15:20:44 UTC  
> Url: https://github.com/boostorg/gil/pull/256  

…n image  
  
using simple filter in the examples folder.  
  
### Tasklist  
  
- [ ] Review  
- [ ] Adjust for comments  
- [ ] All CI builds and checks have passed

---

## Review 1 [Changes requested]

> Username: mloskot  
> Created_at: 2019-03-11 14:18:35 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/gil/pull/256#pullrequestreview-212861212  

Please, include ONLY source code changes in your pull request.  
  
Please, remove all the binary files, generated files or auxiliary files (e.g. `bin.v2` directory)

---

## Review 2 [Changes requested]

> Username: mloskot  
> Created_at: 2019-03-11 14:25:23 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/gil/pull/256#pullrequestreview-212862083  

Thanks for the contribution, but your PR needs some cleaning up first.  
  
Please. re-write your commit message according the Git best practices:  
https://chris.beams.io/posts/git-commit/  
  
That is, clearly separate subject line from description. Keep the subject line up to 50-80 characters  
  
e.g. your commit could be written this way:  
  
```  
Add sample program detecting horizontal and vertical edges  
  
...here describe what technique your example implements,  
name algorithms it uses...  
```

📁 example/simple_filter.cpp

```diff
   1 |+ //
   2 |+ // Copyright 2005-2007 Adobe Systems Incorporated
```

> Username: mloskot  
> Created_at: 2019-03-11 14:19:59 UTC  
> Updated_at: 2019-03-13 21:15:00 UTC  
> Url: https://github.com/boostorg/gil/pull/256#discussion_r264253428  

What is this files about?  
Why you added file that is not copyrighted by yourself?


📁 include/boost/gil/threshold.hpp

```diff
   3 |+ 
   4 |+ template <typename SrcView, typename DstView,typename typ>
   5 |+ void bin_thresh(SrcView const& src, DstView const& dst,typ max,typ min,typ thresh)
```

> Username: mloskot  
> Created_at: 2019-03-11 14:21:07 UTC  
> Updated_at: 2019-03-13 21:15:00 UTC  
> Url: https://github.com/boostorg/gil/pull/256#discussion_r264254001  

In the description of your PR you state that you are proposing a sample application, but here you are adding new feature to the library itself. I'm afraid it is not acceptable.  
  
Please, move this code to the `threshold_demo.cpp` app.


📁 example/threshold_demo.cpp

```diff
   1 |+ #include <boost/gil.hpp>
```

> Username: mloskot  
> Created_at: 2019-03-11 14:21:42 UTC  
> Updated_at: 2019-03-13 21:15:00 UTC  
> Url: https://github.com/boostorg/gil/pull/256#discussion_r264254287  

Please, remove `_demo` suffix from the name. The program is already in `example` folder, so the `_demo` suffix is superfluous.


---

## Comment 3

> Username: sid19991  
> Created_at: 2019-03-13 21:00:39 UTC  
> Url: https://github.com/boostorg/gil/pull/256#issuecomment-472603565  

I have made changes according to your suggestions. Please review the code and suggest if anything more has to be done.

---

## Comment 4

> Username: mloskot  
> Created_at: 2019-03-13 21:07:30 UTC  
> Url: https://github.com/boostorg/gil/pull/256#issuecomment-472605811  

@sid19991  Your PR is still two-commit PR. After cleaning up, you need to do `git commit --amend` to **update** the original commit of the PR. Then, `git push --force`.  
  
Please, updated your PR to make it single-commit with the desired changes only. Then, it will be reviewed.

---
