# #375 - inspect: violation of min/max guidelines and tabs in image_processing/scaling.hpp and numeric/sampler.hpp [Closed]

> Username: mloskot  
> Created at: 2019-08-08 20:12:24 UTC  
> Updated at: 2019-08-09 08:01:11 UTC  
> Closed at: 2019-08-09 08:01:11 UTC  
> Url: https://github.com/boostorg/gil/issues/375  

### Actual behavior  
  
<!-- A clear description of what happens -->  
  
The Boost `inspect` tool reports the following violations:  
  
```  
/mnt/d/boost.wsl/libs/gil$ inspect -text  
Boost Inspection Report  
Run Date: 19:46:33 UTC, Thursday 08 August 2019  
...  
Details:  
  *Tabs* tabs in file  
  *M* uses of min or max that have not been protected from the min/max macros, or unallowed #undef-s  
...  
|include|  
  include/boost/gil/extension/numeric/sampler.hpp:  
    *Tabs*  
  include/boost/gil/image_processing/scaling.hpp:  
    *M* violation of Boost min/max guidelines on line 65  
    *M* violation of Boost min/max guidelines on line 66  
    *M* violation of Boost min/max guidelines on line 69  
    *M* violation of Boost min/max guidelines on line 70  
```  
  
### Expected  behavior  
  
<!-- A clear and concise description of what you expected to happen. -->  
  
The files should not upset the `inspect` tool :-)
