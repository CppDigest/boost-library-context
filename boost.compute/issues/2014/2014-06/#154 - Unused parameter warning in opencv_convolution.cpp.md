# #154 - Unused parameter warning in opencv_convolution.cpp [Closed]

> Username: kylelutz  
> Created at: 2014-06-21 01:55:02 UTC  
> Updated at: 2014-06-25 03:28:19 UTC  
> Closed at: 2014-06-25 03:28:19 UTC  
> Url: https://github.com/boostorg/compute/issues/154  

```  
/home/kyle/dev/compute/example/opencv_convolution.cpp:138:9: warning: unused variable 'filterSize' [-Wunused-variable]  
    int filterSize  = filterWidth*filterWidth;  // Assume a square kernel  
        ^  
```  
  
@Mageswaran1989 can you take a look at this?

---

## Comment 1

> Username: kylelutz  
> Created at: 2014-06-25 03:28:19 UTC  
> Url: https://github.com/boostorg/compute/issues/154#issuecomment-47057033  

Fixed in 3b02c96f664eb87955bb920abcc62dcd68251d9d.
