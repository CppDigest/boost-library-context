# #649 - nth_element_and_half_boxes::apply uses local variable with same name as function parameter [Open]

> Username: AndrewAtAvenza  
> Created at: 2019-11-25 20:05:34 UTC  
> Updated at: 2019-11-25 20:05:34 UTC  
> Url: https://github.com/boostorg/geometry/issues/649  

Compiling 1.69 with VS2019's v142 toolset & arguments /std:c++17 /permissive- I get the following:  
  
[...]boost\include\boost\geometry\index\detail\rtree\pack_create.hpp(80,1):  warning C4457: declaration of 'median' hides function parameter  
  
followed by several pages of further complaints. I found that renaming the local 'median' to something like 'edge_median' and updating the two usages of the variable (next two lines) the warning went away.
