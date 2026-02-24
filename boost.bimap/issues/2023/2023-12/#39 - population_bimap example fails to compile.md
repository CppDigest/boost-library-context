# #39 - population_bimap example fails to compile [Open]

> Username: jefftrull  
> Created at: 2023-12-07 19:12:53 UTC  
> Updated at: 2023-12-07 19:12:53 UTC  
> Url: https://github.com/boostorg/bimap/issues/39  

gcc 11.4 fails to compile this example with an error `template argument 1 is invalid` on this line:  
  
https://github.com/boostorg/bimap/blob/e5c2657a9e2d6184622ad4ccd1373e6c60a7efca/example/population_bimap.cpp#L46  
  
Specifying the Boost namespace on `optional` fixes it (you cannot use std here).
