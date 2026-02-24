# #391 - Allow all the `boundary_option` in `extend_boundary` [Closed]

> Username: lpranam  
> Created at: 2019-09-17 21:55:09 UTC  
> Updated at: 2019-10-28 16:19:28 UTC  
> Closed at: 2019-10-28 16:19:28 UTC  
> Url: https://github.com/boostorg/gil/issues/391  

After discussion from #387 and PR #390 now a unified class for boundary option exist in `numeric/algorithm.hpp` named `boundary_option`.   
  
This class is also used in `extend_boundary` but due to unification new options are added in `boundary_option` which are not handled by this function.   
  
Implementation of this function needs to be expanded to handle new options :  
1. `output_ignore`  
2. `output_zero`  
3. `extend_padded`
