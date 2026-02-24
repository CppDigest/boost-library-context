# #376 - inspect: violation of min/max guidelines in image_processing/threshold.hpp [Closed]

> Username: mloskot  
> Created at: 2019-08-08 20:13:20 UTC  
> Updated at: 2019-08-09 15:36:38 UTC  
> Closed at: 2019-08-09 15:36:38 UTC  
> Url: https://github.com/boostorg/gil/issues/376  

### Actual behavior  
  
<!-- A clear description of what happens -->  
  
The Boost `inspect` tool reports the following violations:  
  
```  
/mnt/d/boost.wsl/libs/gil$ inspect -text  
Boost Inspection Report  
Run Date: 19:46:33 UTC, Thursday 08 August 2019  
...  
Details:  
  *M* uses of min or max that have not been protected from the min/max macros, or unallowed #undef-s  
...  
|include|  
  include/boost/gil/image_processing/threshold.hpp:  
    *M* violation of Boost min/max guidelines on line 156  
    *M* violation of Boost min/max guidelines on line 233  
    *M* violation of Boost min/max guidelines on line 234  
    *M* violation of Boost min/max guidelines on line 448  
```  
  
### Expected  behavior  
  
<!-- A clear and concise description of what you expected to happen. -->  
  
The files should not upset the `inspect` tool :-)
