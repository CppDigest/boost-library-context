# #348 - Add KernelConcept to document and check kernel requirements [Open]

> Username: mloskot  
> Created at: 2019-07-22 09:36:03 UTC  
> Updated at: 2019-07-22 09:36:03 UTC  
> Url: https://github.com/boostorg/gil/issues/348  

Following some brief discussion in #347, `kernel_2d` and `kernel_2d_fixed` defined in Numeric may not be as well documented as users may expect. For example,  
  
- Are the kernel types singular or dual mode: 1D and/or 2D  
- Since they are used in both, 1D and 2D convolution operations, are there any particular requirements and restrictions for each of use cases?  
  
Ideally, if there are `KernelConcept`-s that capture all the details.
