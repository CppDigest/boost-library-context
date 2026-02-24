# #549 - Move documentation deployment to GitHub Actions [Open]

> Username: mloskot  
> Created at: 2021-01-25 23:51:25 UTC  
> Updated at: 2021-03-02 10:02:22 UTC  
> Url: https://github.com/boostorg/gil/issues/549  

We are going to remove Travis CI configuration and we need to move the `DOC` build job from Travis CI to GitHub Actions,  
ideally as a separate dedicated workflow.  
  
Shortly, it just needs to replicate steps that we used to run by the scripts that have been removed in commit https://github.com/boostorg/gil/commit/e3e779cc69af4905e4910c06f7e9417c2563e362  
- `.ci/upload_docs.sh`  
- `.travis.yml`  
  
and that's pretty much it, but  on GitHbu Actions.  
  
### Tasklist  
  
- [x] in develop branch  
- [ ] in master branch
