# #40 - Examples in develop branch use old IO extension [Closed]

> Username: mloskot  
> Created at: 2018-03-14 16:54:36 UTC  
> Updated at: 2018-12-18 00:13:11 UTC  
> Closed at: 2018-12-18 00:13:11 UTC  
> Url: https://github.com/boostorg/gil/issues/40  

There examples in the current **develop** branch have not been ported to the new IO v2 extension, so they still include the old IO headers. For example:  
  
```  
dynamic_image.cpp(20):#include <boost/gil/extension/io/jpeg_dynamic_io.hpp>  
interleaved_ptr.cpp(35):#include <boost/gil/extension/io/jpeg_dynamic_io.hpp>  
x_gradient.cpp(18):#include <boost/gil/extension/io/jpeg_dynamic_io.hpp>  
```  
  
```  
affine.cpp(25):#include <boost/gil/extension/io/jpeg_io.hpp>  
convolution.cpp(25):#include <boost/gil/extension/io/jpeg_io.hpp>  
histogram.cpp(23):#include <boost/gil/extension/io/jpeg_io.hpp>  
mandelbrot.cpp(20):#include <boost/gil/extension/io/jpeg_io.hpp>  
packed_pixel.cpp(36):#include <boost/gil/extension/io/jpeg_io.hpp>  
resize.cpp(25):#include <boost/gil/extension/io/jpeg_io.hpp>  
```

---

## Comment 1

> Username: chhenning  
> Created at: 2018-03-20 15:02:51 UTC  
> Url: https://github.com/boostorg/gil/issues/40#issuecomment-374630959  

We can remove those tests. I think I have added the tests to ensure the new io is backwards compatible. Meaning the old io's interface is still functioning with the new io.

---

## Comment 2

> Username: mloskot  
> Created at: 2018-03-20 15:10:14 UTC  
> Url: https://github.com/boostorg/gil/issues/40#issuecomment-374633772  

If there are no equivalents based on IO v2, I think it's worth to port them.

---

## Comment 3

> Username: chhenning  
> Created at: 2018-03-20 15:19:55 UTC  
> Url: https://github.com/boostorg/gil/issues/40#issuecomment-374637207  

Oh, I think I misunderstood you. You're not talking about tests but actual library code. Let me see

---

## Comment 4

> Username: mloskot  
> Created at: 2018-03-20 15:25:51 UTC  
> Url: https://github.com/boostorg/gil/issues/40#issuecomment-374639421  

@chhenning I think I've got confused myself to: I reported *exampels* you responded *those tests* :)  
  
Let's rewind and talk about the examples (resize, mandelbrot, etc.). I suggest that instead of removing them, they should be ported to the new IO.

---

## Comment 5

> Username: chhenning  
> Created at: 2018-03-20 15:29:26 UTC  
> Url: https://github.com/boostorg/gil/issues/40#issuecomment-374640747  

I agree. We can make them part of the test suite.

---

## Comment 6

> Username: chhenning  
> Created at: 2018-03-20 15:33:41 UTC  
> Url: https://github.com/boostorg/gil/issues/40#issuecomment-374642419  

I didn't realize there are "Examples". Might be good to include them in the documentation.

---

## Comment 7

> Username: mloskot  
> Created at: 2018-03-20 15:38:32 UTC  
> Url: https://github.com/boostorg/gil/issues/40#issuecomment-374644354  

The ancient GIL docs http://stlab.adobe.com/gil/documentation.html include them in the list of Samples on the front page. BTW, this old docs should be put down as they are doing bad publicity for Boost.GIL, IMHO.
