# #245 - b2 warning: non-free usage requirements <boost.cobalt.pmr>std ignored [Open]

> Username: Lastique  
> Created at: 2025-09-07 11:05:10 UTC  
> Updated at: 2025-09-07 11:05:10 UTC  
> Url: https://github.com/boostorg/cobalt/issues/245  

When building Boost 1.89.0 with the following command line:  
  
```  
b2 -j 16 --toolset=gcc cxxflags="-flto=auto -ffat-lto-objects -march=nehalem -mtune=icelake-server -faligned-new -fcoroutines -fno-plt -fvisibility-inlines-hidden -O3 -fno-math-errno -fno-rounding-math -fno-signaling-nans -fno-trapping-math -ftree-vectorize -fgcse-sm -fgcse-las -fgcse-after-reload -ftree-loop-im -ftree-loop-distribution -ftree-loop-if-convert -funswitch-loops -frename-registers -fivopts" linkflags="-Wl,-z,relro -Wl,-z,now -Wl,-O1 -Wl,--hash-style=both -Wl,-z,noexecstack -Wl,--as-needed" \  
 variant=release cxxstd=20-gnu threading=multi debug-symbols=on runtime-link=shared optimization=speed inlining=full warnings=on link=shared --without-mpi --without-wave --without-python --without-graph stage  
```  
  
`b2` displays this warning during the configuration process:  
  
```  
warning: non-free usage requirements <boost.cobalt.pmr>std ignored  
warning: in main-target boost_cobalt at /build/boost/boost/libs/cobalt/build/Jamfile:75  
```
