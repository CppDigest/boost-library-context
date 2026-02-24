# #678 Add Wrapper to clfft clblas ... [Closed]

> Username: mahdimcheik  
> Created at: 2016-12-23 16:04:15 UTC  
> Updated at: 2017-03-21 05:02:25 UTC  
> Closed at: 2017-03-21 03:03:00 UTC  
> Url: https://github.com/boostorg/compute/pull/678  

Hello,  
The problem with OpenCL is that there is no complete library compared to cuda where all libraries like cufft and cublas are installed by default,  but with OpenCL  we have to search equivalent libraries and try to figure out a way to link them with other libraries we may use in the same program. I think that compute boost is a good apportunity  to create a full library cuda-like. Libraries like Arrayfire are not standard and dont give full control as in native OpenCL c/c++ program without lose of performance.  
So can please you add wrapper "boost-like" of those libraries clfft clblas clsparse , clrand and maybe cl deep learning if that exist, where the programming is a kind unified under the boost model.  
sorry for my english and thank you if you still red till here

---

## Comment 1

> Username: coveralls  
> Created_at: 2016-12-23 17:10:30 UTC  
> Url: https://github.com/boostorg/compute/pull/678#issuecomment-269019376  

[![Coverage Status](https://coveralls.io/builds/9411853/badge)](https://coveralls.io/builds/9411853)  
  
Coverage increased (+0.01%) to 83.351% when pulling **cc30762666aa9b20f62751cdf119f2cf1a103532 on develop** into **82f588f24ed2640bca4bf3f31dfcb06703d9bc13 on master**.

---

## Comment 2

> Username: kylelutz  
> Created_at: 2017-03-21 03:03:00 UTC  
> Url: https://github.com/boostorg/compute/pull/678#issuecomment-287963153  

Hi @mahdimcheik,  
  
Looks like this is a feature request, would you mind closing this and opening an issue with the "feature" tag on the [issue tracker](https://github.com/boostorg/compute/issues).  
  
Thanks!

---

## Comment 3

> Username: coveralls  
> Created_at: 2017-03-21 05:02:25 UTC  
> Url: https://github.com/boostorg/compute/pull/678#issuecomment-287977775  

[![Coverage Status](https://coveralls.io/builds/10693455/badge)](https://coveralls.io/builds/10693455)  
  
Coverage increased (+0.01%) to 83.351% when pulling **d2132e4ce5cdf9ebea782828d2bb8a510c36bb9c on develop** into **82f588f24ed2640bca4bf3f31dfcb06703d9bc13 on master**.

---
