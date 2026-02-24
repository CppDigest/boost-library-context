# #267 - Add slicing support for category axis [Closed]

> Username: HDembinski  
> Created at: 2020-01-03 15:37:33 UTC  
> Updated at: 2020-01-06 00:40:01 UTC  
> Closed at: 2020-01-06 00:40:01 UTC  
> Url: https://github.com/boostorg/histogram/issues/267  

category axes are currently not sliceable (see reduce algorithm), because slicing didn't seem to make sense for category axes.  
  
However,  
https://github.com/scikit-hep/boost-histogram/issues/296  
suggests that also category axis should be sliceable, and eventually one should be able to pick individual bin indices as well. Perhaps using the keyword `pick(index1, index2, ...)` as suggested by @henryiii

---

## Comment 1

> Username: henryiii  
> Created at: 2020-01-03 21:24:47 UTC  
> Url: https://github.com/boostorg/histogram/issues/267#issuecomment-570702674  

Note on pick: you need both an index and an axes number, since the difference with `.at()` is that you will leave some axes alone and get a histogram back. Or some way to indicate that an axes should be kept. It would be quite useful for integer (+boolean) axes and category axes, I believe.

---

## Comment 2

> Username: HDembinski  
> Created at: 2020-01-05 18:39:49 UTC  
> Url: https://github.com/boostorg/histogram/issues/267#issuecomment-570936415  

The patch for making category axis sliceable is almost done  
https://github.com/HDembinski/histogram/pull/103

---

## Comment 3

> Username: HDembinski  
> Created at: 2020-01-06 00:40:01 UTC  
> Url: https://github.com/boostorg/histogram/issues/267#issuecomment-570965920  

closed in develop
