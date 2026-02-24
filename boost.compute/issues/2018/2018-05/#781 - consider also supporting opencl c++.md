# #781 - consider also supporting opencl c++ [Open]

> Username: maddanio  
> Created at: 2018-05-16 22:20:01 UTC  
> Updated at: 2018-12-30 12:23:12 UTC  
> Url: https://github.com/boostorg/compute/issues/781  

i know this is brand new, but opencl is going for a c++ superset, like cuda:  
https://www.khronos.org/assets/uploads/developers/resources/Intro-to-OpenCL-C++-Whitepaper-May15.pdf  
also this will hopefully land in clang soon:  
https://www.phoronix.com/scan.php?page=news_item&px=OpenCL-CXX-Clang-Interest  
would boost::compute be interested in supplying tools for kernel programming, like thrust?

---

## Comment 1

> Username: maddanio  
> Created at: 2018-05-16 22:20:33 UTC  
> Url: https://github.com/boostorg/compute/issues/781#issuecomment-389684215  

i.e. library code that can be used inside the kernels themselves

---

## Comment 2

> Username: jszuppe  
> Created at: 2018-05-16 22:44:01 UTC  
> Updated at: 2018-05-16 22:44:50 UTC  
> Url: https://github.com/boostorg/compute/issues/781#issuecomment-389689278  

OpenCL C++ is not quite like CUDA. It still isn't single source, you can't have a kernel template etc. Major GPU vendors do not support SPIR-V (for OpenCL), thus they do not support OpenCL 2.2 and OpenCL C++ kernel language.  
  
Nonetheless, using OpenCL C++ would solve a lot of our current problems with OpenCL + C++ (ctors, dtors, operator overloading). However, that would require some major changes: refactoring, improving, rewriting algorithms. I don't know if there is someone with free time to do it. Also, a lot of people use (and will use) hardware with support only for OpenCL C, so updated Boost.Compute should still support it.

---

## Comment 3

> Username: jszuppe  
> Created at: 2018-05-16 22:47:08 UTC  
> Url: https://github.com/boostorg/compute/issues/781#issuecomment-389689892  

To be honest, before any major update I would wait for OpenCL Next with feature sets.

---

## Comment 4

> Username: maddanio  
> Created at: 2018-05-18 09:11:42 UTC  
> Url: https://github.com/boostorg/compute/issues/781#issuecomment-390146393  

sure, not talking about rushing things at all. just thought when c++ comes to kernels, boost should be there too, with kernel specific  tools. did not  know that spir-v support is slow. but even clang support ist still in the coming, so yeah, its early, but exciting :)

---

## Comment 5

> Username: jszuppe  
> Created at: 2018-05-18 09:43:19 UTC  
> Url: https://github.com/boostorg/compute/issues/781#issuecomment-390154580  

If you want to have something like Thrust (single source), we would have to add SYCL backend.
