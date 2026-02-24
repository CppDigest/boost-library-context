# #157 - cpp_ndarray.shape(-1) should behave like python_ndarray.shape[-1] and boost::python::tuple::operator[](-1) [Closed]

> Username: mborgerding  
> Created at: 2017-09-19 18:36:11 UTC  
> Updated at: 2017-10-04 20:40:37 UTC  
> Closed at: 2017-10-04 20:40:37 UTC  
> Url: https://github.com/boostorg/python/issues/157  

Currently `ndarray::shape(int n)` returns `get_shape()[n]`, which indexes a raw memory buffer (C-style array). This results in undefined behavior under the python idiom of negative indices.  
  
I propose `shape` ( and similarly `strides`) performs bounds checking and indexing from the back for negatives.

---

## Comment 1

> Username: stefanseefeld  
> Created at: 2017-09-19 19:43:32 UTC  
> Url: https://github.com/boostorg/python/issues/157#issuecomment-330651977  

That sounds reasonable. Care to submit a patch ? ;-)

---

## Comment 2

> Username: mborgerding  
> Created at: 2017-09-20 13:56:32 UTC  
> Url: https://github.com/boostorg/python/issues/157#issuecomment-330859449  

There is a failed CI build, but I cannot see any resemblence to my changes.  
  
`unresolved external symbol  ... boost::get_pointer<struct test_class<0> ...`

---

## Comment 3

> Username: stefanseefeld  
> Created at: 2017-09-20 14:09:21 UTC  
> Url: https://github.com/boostorg/python/issues/157#issuecomment-330863349  

Right, I know. (The problem is that the build system I use is broken, too, as the whole test run fails rather than just flagging the failed test. I'm working on a new build system to supersede the SCons-based one.)  
Don't worry about that. I'm just waiting for the Travis build to finish. Your patch looks good, and I'm looking forward to merging it once it's confirmed that the new tests pass and no regressions are introduced. Thanks !
