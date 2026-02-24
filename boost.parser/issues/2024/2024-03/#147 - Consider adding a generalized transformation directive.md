# #147 - Consider adding a generalized transformation directive [Open]

> Username: tzlaine  
> Created at: 2024-03-01 00:14:44 UTC  
> Updated at: 2024-03-05 01:25:28 UTC  
> Url: https://github.com/boostorg/parser/issues/147  

From #119:  
  
> Maybe something for the future: there's a lot of effort in the code to get no_case working, and in  
> the end it's all about applying case folding. What about having a transformation-directive letting  
> users plug-in their own transformations for input and target:  
>   
> `transform( _input_transformation_ )[ parser ]`  
> `transform( _input_transformation_, _target_transformation_ )[ parser ]`

---

## Comment 1

> Username: tzlaine  
> Created at: 2024-03-05 01:25:27 UTC  
> Url: https://github.com/boostorg/parser/issues/147#issuecomment-1977774854  

This is very low priority.  I like the idea, but it's currently searching for a use case.  I'm leaving it open so that 1) it is not forgotten, and 2) others who *do* have a use case for this might stumble upon it.
