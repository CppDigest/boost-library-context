# #71 - OpenCL tests failing on travis [Closed]

> Username: coder3101  
> Created at: 2019-05-28 07:07:00 UTC  
> Updated at: 2019-07-11 06:46:00 UTC  
> Closed at: 2019-07-11 06:46:00 UTC  
> Url: https://github.com/boostorg/ublas/issues/71  

All the unit tests requiring OpenCL are failing on Travis as a result the builds are failing. The following information may help fix the problem.   
  
- Reason for failure : **No OpenCL Device Found**  
- Build Environment : **trusty 14.04 LTS**  
- Extra : In recent blog post of Travis, they mentioned that they are migrating to Xenial 16.04 as trusty's support ended on April 30.   
  
`boost::compute` is used by uBLAS for OpenCL based computation. The possible reason for failure could be change in build environment by Travis or Invalid installation of OpenCL. All the branches are affected by this, master branch is also likely to fall.

---

## Comment 1

> Username: coder3101  
> Created at: 2019-07-11 06:46:00 UTC  
> Url: https://github.com/boostorg/ublas/issues/71#issuecomment-510352957  

Builds are passing. It was an Issue with travis
