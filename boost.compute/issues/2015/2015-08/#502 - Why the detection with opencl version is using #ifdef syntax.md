# #502 - Why the detection with opencl version is using #ifdef  syntax? [Closed]

> Username: swt02026  
> Created at: 2015-08-28 19:23:55 UTC  
> Updated at: 2015-08-29 15:34:19 UTC  
> Closed at: 2015-08-29 11:16:08 UTC  
> Url: https://github.com/boostorg/compute/issues/502  

My English isn't very good, please don't mind.  
  
I use image2d object in my source but I get "segmentation fault" error.  
  
I found the cl.h installed in my compute is defined from CL_VERSION_1_0 to CL_VERSION_1_2 inside the source code.  
  
My opencl driver and header is NVIDIA's card, I don't know why.  
  
But I found khronos opencl c++ wrapper using "#if defined()" syntax to detect opencl version, it will success to use Image2D in this wrapper.  
(but i need to modify CL_VERSION_1_2 macro from 1 to 0 inside cl.h)  
  
Thanks.

---

## Comment 1

> Username: swt02026  
> Created at: 2015-08-28 19:29:04 UTC  
> Updated at: 2015-08-29 04:00:56 UTC  
> Url: https://github.com/boostorg/compute/issues/502#issuecomment-135867098  

before i change my cl.h  
![before](https://cloud.githubusercontent.com/assets/5100749/9555138/e6007738-4dfd-11e5-8b37-3af126d9433f.png)  
  
after i change my cl.h  
![after](https://cloud.githubusercontent.com/assets/5100749/9555146/f2ad357a-4dfd-11e5-8ce2-15da8e06e156.png)  
My OpenCL version is 1.1  
is nvidia not support opencl 1.2 ?   
Why they define CL_VERSION_1_2 in the cl.h?

---

## Comment 2

> Username: kylelutz  
> Created at: 2015-08-29 15:34:19 UTC  
> Url: https://github.com/boostorg/compute/issues/502#issuecomment-136000769  

Yeah, until recently, NVIDIA only supported OpenCL v1.1. You should ensure that the OpenCL headers you compile against match up with the OpenCL version supported by the vendor/library you use at run-time.
