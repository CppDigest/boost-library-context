# #142 - static_size is not a member of boost::gil::kernel_1d_fixed [Closed]

> Username: mloskot  
> Created at: 2018-09-21 20:18:22 UTC  
> Updated at: 2018-12-18 10:57:33 UTC  
> Closed at: 2018-12-18 10:57:33 UTC  
> Url: https://github.com/boostorg/gil/issues/142  

/cc @chhenning, @stefanseefeld   
  
Apart from required I/O updates, [convolution.cpp](https://github.com/boostorg/gil/blob/ecc19de7c9a77854c19d40b2d0a4083c1d922aa9/example/convolution.cpp) example does not compile:  
  
```  
convolution.cpp:74:85:   required from here                                                                                                   
../../../boost/gil/extension/numeric/convolve.hpp:187:43:  
  error: ‘static_size’ is not a member of ‘boost::gil::kernel_1d_fixed<float, 9ul>’   
     detail::correlate_rows_imp<PixelAccum>(src,ker,dst,option,detail::correlator_k<Kernel::static_size,PixelAccum>());                       
```  
  
The Numeric extension uses `Kernel::static_size` which seems indeed not defined anywhere:  
  
https://github.com/boostorg/gil/blob/18d5ecb7a82b653c25295049b2a7ff28cad12c39/include/boost/gil/extension/numeric/convolve.hpp#L183-L188  
  
Who knows where did the `static_size` used to come from?  
  
## References  
  
* #40
