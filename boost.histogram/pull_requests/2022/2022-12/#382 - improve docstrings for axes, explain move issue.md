# #382 improve docstrings for axes, explain move issue [Merged]

> Username: HDembinski  
> Created at: 2022-12-26 12:14:43 UTC  
> Updated at: 2022-12-26 13:41:19 UTC  
> Merged at: 2022-12-26 13:41:18 UTC  
> Closed at: 2022-12-26 13:41:18 UTC  
> Url: https://github.com/boostorg/histogram/pull/382  

Close #379   
  
We cannot provide the strong exception guarantee without making Metadata a template parameter in very ctor, so the issue is documented instead. The patch also clarifies that the metadata must be default constructible.

---
