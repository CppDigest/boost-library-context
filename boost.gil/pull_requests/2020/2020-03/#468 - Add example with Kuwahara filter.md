# #468 Add example with Kuwahara filter [Open]

> Username: martin-carrasco  
> Created at: 2020-03-29 07:28:40 UTC  
> Updated at: 2022-06-03 06:52:39 UTC  
> Url: https://github.com/boostorg/gil/pull/468  

# Description  
  
Added an implementation of the Kuwahara adaptive filter working for grayscale images  
  
This is part of my competency test solution for GSoC 2020. I would appreciate any feedback or comments.  
  
### References  
  
https://d-nb.info/1094419265/34  
  
### Further Development  
+ Implement an channel agnostic version and add to filter.hpp  
+ Document   
### Tasklist  
  
- [ ] Add test case(s)  
- [ ] Ensure all CI builds pass  
- [ ] Review and approve

---

## Comment 1

> Username: mloskot  
> Created_at: 2020-03-30 09:22:37 UTC  
> Updated_at: 2020-03-30 09:24:56 UTC  
> Url: https://github.com/boostorg/gil/pull/468#issuecomment-605884960  

Thanks for the example. For the competency test it looks good.  
For submission to GIL as example, it needs copyright notice, Jamfile/CMakeLists.txt update, some formatting (even minimal using the [.clang-format](https://github.com/boostorg/gil/tree/develop/example/clang-format)),  according to GIL guidelines. However, it will not be accepted before the GSoC students selection is completed.

---
