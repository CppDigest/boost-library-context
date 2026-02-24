# #207 - Visual Studio 2017 generates invalid code for caller.hpp [Open]

> Username: SPKorhonen  
> Created at: 2018-05-30 08:01:16 UTC  
> Updated at: 2019-03-04 03:06:29 UTC  
> Url: https://github.com/boostorg/python/issues/207  

Under Python 3.6.5 and Visual Studio 2017 (15.7.2) and optimization level /O2 compilation of boost::python generates invalid code while compiling ".\boost\python\detail\caller.hpp". Namely, initialization of "static const signature_element ret" (line 245->) is erroneously skipped leading to SegFault in python.exe.

---

## Comment 1

> Username: stefanseefeld  
> Created at: 2019-03-04 03:06:29 UTC  
> Url: https://github.com/boostorg/python/issues/207#issuecomment-469102841  

I have just merged #208 into the `develop` branch, and intend to include it into the upcoming `1.70` release. Would you mind testing again against `develop`, so I can close this issue ? Thanks !
