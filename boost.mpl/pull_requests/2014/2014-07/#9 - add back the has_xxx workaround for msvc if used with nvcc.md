# #9 add back the has_xxx workaround for msvc if used with nvcc [Merged]

> Username: ericniebler  
> Created at: 2014-07-07 22:50:46 UTC  
> Updated at: 2014-07-07 23:36:08 UTC  
> Merged at: 2014-07-07 23:36:08 UTC  
> Closed at: 2014-07-07 23:36:08 UTC  
> Url: https://github.com/boostorg/mpl/pull/9  

In boostorg/mpl#8 I scoped a workaround to the affected versions of msvc. It turns out that breaks CUDA builds with nvcc. This fine-tunes the workaround for that case. I would dearly love this to be in the upcoming release.

---
