# #7 Generate meta viewport element. [Merged]

> Username: vprus  
> Created at: 2014-12-08 17:31:22 UTC  
> Updated at: 2023-02-26 14:28:51 UTC  
> Merged at: 2023-02-26 14:28:51 UTC  
> Closed at: 2023-02-26 14:28:51 UTC  
> Url: https://github.com/boostorg/boostbook/pull/7  

This improves layout on mobile devices. I had to remove declaration  
for the 'rev' element as otherwise the 'meta' element would have  
namespace declaration as well, for no reason. Possibly there's a  
better solution.

---
