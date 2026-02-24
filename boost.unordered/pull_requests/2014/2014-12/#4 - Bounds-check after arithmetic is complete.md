# #4 Bounds-check after arithmetic is complete [Merged]

> Username: tempoz  
> Created at: 2014-12-08 21:38:51 UTC  
> Updated at: 2014-12-09 01:04:30 UTC  
> Merged at: 2014-12-09 01:04:30 UTC  
> Closed at: 2014-12-09 01:04:30 UTC  
> Url: https://github.com/boostorg/unordered/pull/4  

double_to_size can return std::numeric_limits<size_t>max(), so we cannot add 1 to the return value of double_to_size. That addition should be done while still working with a double, as can be seen being done on line 850 of this file.  
This was uncovered by Coverity, and addresses Coverity issues CID13443 and CID12664

---
