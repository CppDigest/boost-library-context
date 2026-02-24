# #137 - add hexagonal binning [Open]

> Username: HDembinski  
> Created at: 2018-12-13 14:46:33 UTC  
> Updated at: 2023-06-07 17:55:41 UTC  
> Url: https://github.com/boostorg/histogram/issues/137  

add a 2d axis which does hexagonal binning  
  
Everything about hexagonal grids:  
https://www.redblobgames.com/grids/hexagons/

---

## Comment 1

> Username: ryanelandt  
> Created at: 2023-06-07 17:55:41 UTC  
> Url: https://github.com/boostorg/histogram/issues/137#issuecomment-1581267783  

After axis growing, the values in the old bins need to be assigned into the new bins. I think the way this is currently done [here](https://github.com/boostorg/histogram/blob/a29729e6682652800b06dc8aded29bbeb4acea61/include/boost/histogram/detail/fill.hpp#L87) uses an integer shift for each axis to calculate the new index for each bin. This approach will require modification/generalization to support rebinning a 2D axis. Do you envision a 2D hexagonal axis type supporting axis growing?
