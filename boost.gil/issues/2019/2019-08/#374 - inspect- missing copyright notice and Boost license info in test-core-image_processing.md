# #374 - inspect: missing copyright notice and Boost license info in test/core/image_processing/ [Closed]

> Username: mloskot  
> Created at: 2019-08-08 19:52:15 UTC  
> Updated at: 2019-08-09 08:01:11 UTC  
> Closed at: 2019-08-09 08:01:11 UTC  
> Url: https://github.com/boostorg/gil/issues/374  

### Actual behavior  
  
<!-- A clear description of what happens -->  
  
The Boost `inspect` tool reports the following violations in `test/core/image_processing/*`:  
  
```  
/mnt/d/boost.wsl/libs/gil$ inspect -text  
Boost Inspection Report  
Run Date: 19:46:33 UTC, Thursday 08 August 2019  
...  
Details:  
  *Lic* missing Boost license info, or wrong reference text  
  *C* missing copyright notice  
...  
test/core/image_processing/lanczos_scaling.cpp:  
    *C*  
    *Lic*  
  test/core/image_processing/simple_kernels.cpp:  
    *C*  
    *Lic*  
```  
  
### Expected  behavior  
  
<!-- A clear and concise description of what you expected to happen. -->  
  
The files should have the copyright and license added.
