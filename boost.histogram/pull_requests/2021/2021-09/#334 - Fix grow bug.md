# #334 Fix grow bug [Merged]

> Username: HDembinski  
> Created at: 2021-09-26 13:44:13 UTC  
> Updated at: 2021-09-26 16:31:36 UTC  
> Merged at: 2021-09-26 16:31:36 UTC  
> Closed at: 2021-09-26 16:31:36 UTC  
> Url: https://github.com/boostorg/histogram/pull/334  

Closes #327   
  
This was a bug deep in the implementation logic, not in the general logic of how to handle the various cases of growing and not growing axes and the broadcasting logic. The code assumed that two iterators point to the same array, while some outer code in some cases created to iterators that pointed to two different "arrays".

---
