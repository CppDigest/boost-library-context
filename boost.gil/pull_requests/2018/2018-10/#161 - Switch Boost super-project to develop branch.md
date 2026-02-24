# #161 [travis] Switch Boost super-project to develop branch [Merged]

> Username: mloskot  
> Created at: 2018-10-23 15:59:17 UTC  
> Updated at: 2018-10-23 19:52:03 UTC  
> Merged at: 2018-10-23 19:51:52 UTC  
> Closed at: 2018-10-23 19:51:52 UTC  
> Url: https://github.com/boostorg/gil/pull/161  

Currently, we are experiencing UBSan builds failures due to issues in Boost.Function version attached to Boost super-project.  
Build UBSan with b2 `visibility=global` instead of default `hidden`.  
  
Detailed discussions at:  
- https://github.com/boostorg/function/pull/29  
- https://lists.boost.org/boost-gil/2018/10/0100.php  
  
Note, we are switching temporarily and we should switch back to testing against Boost super-project master branch - a stable Boost version closest to the next release state.  
  
### Tasklist  
  
- [x] All CI builds and checks have passed  
  
-----  
  
I've got Travis CI for my fork at https://travis-ci.org/mloskot/gil which starts and completes the builds much quicker than for the org at https://travis-ci.org/boostorg/gil/

---

## Comment 1

> Username: mloskot  
> Created_at: 2018-10-23 19:51:59 UTC  
> Url: https://github.com/boostorg/gil/pull/161#issuecomment-432394132  

The Travis builds for my fork passed - https://travis-ci.org/mloskot/gil/builds/445285772 - so the PR fixes the problem. Since it may take long hours to get the builds for boostorg start, I'm merging this and updating pending PRs.

---
