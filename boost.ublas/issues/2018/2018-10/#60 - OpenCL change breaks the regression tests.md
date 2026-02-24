# #60 - OpenCL change breaks the regression tests [Closed]

> Username: pdimov  
> Created at: 2018-10-29 19:34:06 UTC  
> Updated at: 2019-03-04 07:01:06 UTC  
> Closed at: 2019-03-04 07:01:06 UTC  
> Url: https://github.com/boostorg/ublas/issues/60  

`b2` in `status` says:  
  
```  
opencl.jam: No such file or directory  
/home/travis/build/boostorg/boost/tools/build/src/build/toolset.jam:44: in toolset.using  
ERROR: rule "opencl.init" unknown in module "toolset".  
/home/travis/build/boostorg/boost/tools/build/src/build/project.jam:1102: in using  
../libs/numeric/ublas/test/opencl/Jamfile:11: in modules.load  
```  
  
https://travis-ci.org/boostorg/boost/jobs/447771259#L7244

---

## Comment 1

> Username: bassoy  
> Created at: 2019-03-04 07:01:06 UTC  
> Url: https://github.com/boostorg/ublas/issues/60#issuecomment-469140611  

This is solved I think. Thanks for the comment.
