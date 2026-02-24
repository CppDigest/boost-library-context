# #488 - kernel_2d returns incorrect vertical and horizontal size [Closed]

> Username: lpranam  
> Created at: 2020-04-25 17:22:01 UTC  
> Updated at: 2020-04-25 17:55:56 UTC  
> Closed at: 2020-04-25 17:55:19 UTC  
> Url: https://github.com/boostorg/gil/issues/488  

https://github.com/boostorg/gil/blob/cee21c261cbc658b822ef419beb0f090c81e9a40/include/boost/gil/extension/numeric/kernel.hpp#L195  
  
In kernel_2d_adapter vertical size (i.e: `lower_size` and `upper_size`) are interchanged with horizontal size (i.e: `right_size` and `left_size`) resulting in returning incorrect values.

---

## Comment 1

> Username: lpranam  
> Created at: 2020-04-25 17:55:19 UTC  
> Url: https://github.com/boostorg/gil/issues/488#issuecomment-619417319  

Sorry this was a huge blunder caused by a mistake :(

---

## Comment 2

> Username: mloskot  
> Created at: 2020-04-25 17:55:43 UTC  
> Url: https://github.com/boostorg/gil/issues/488#issuecomment-619417375  

No problem, double-checking does not hurt!
