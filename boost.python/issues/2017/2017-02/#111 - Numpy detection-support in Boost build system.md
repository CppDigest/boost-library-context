# #111 - Numpy detection/support in Boost build system [Closed]

> Username: 1pakch  
> Created at: 2017-02-16 19:58:51 UTC  
> Updated at: 2017-02-16 21:04:10 UTC  
> Closed at: 2017-02-16 21:04:10 UTC  
> Url: https://github.com/boostorg/python/issues/111  

I am building Boost 1.63 (all libraries) as a nix package on a Linux machine. Depending on how I supply numpy to the build environment the `libboost_numpy` is either built or not (in all cases it is available to the relevant interpreter via `import numpy`).  
  
I am not familiar with boost jam build system and find it very difficult to understand how it decides whether to build the numpy extension or not. Hence I would be grateful if someone could point me in the right direction. I could contribute to documenting that aspect of the build system as it is very well warranted IMO.

---

## Comment 1

> Username: stefanseefeld  
> Created at: 2017-02-16 20:20:30 UTC  
> Url: https://github.com/boostorg/python/issues/111#issuecomment-280447482  

For my own work I'm actually moving away from Boost.Build, as I want to build Boost.Python standalone (against a pre-installed Boost). For that I'm using `SCons`, With that, the build logic is set up to build with NumPy support whenever that is detected. To not build libboost_numpy even if NumPy was detected, you can use the `--no-numpy` option.  
  
I don't think Boost.Build (b2) right now offers an option to disable libboost_numpy, so it would be built whenever NumPy is found.  
You seem to suggest that that isn't the case, i.e. you observe `b2` not building libboost_numpy, even though NumPy is installed. That would be a bug worth reporting. (If you want to debug this yourself, the relevant logic is in https://github.com/boostorg/build/blob/develop/src/tools/python.jam#L833-L851

---

## Comment 2

> Username: 1pakch  
> Created at: 2017-02-16 21:04:10 UTC  
> Url: https://github.com/boostorg/python/issues/111#issuecomment-280459856  

Many thanks Stefan, with your help I was able to quickly find the relevant debug messages (via `./b2 -d 5 -n|grep -i -A 2 'python.debug-message'` if someone will be looking here). There is no bug, the python interpreter supplied to the build system indeed had no numpy.  
  
I also feel that for my work it's better to build Boost.Python against an existing Boost and I will probably switch to that.
