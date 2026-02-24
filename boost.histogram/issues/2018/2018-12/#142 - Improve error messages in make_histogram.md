# #142 - Improve error messages in make_histogram [Closed]

> Username: HDembinski  
> Created at: 2018-12-17 15:06:45 UTC  
> Updated at: 2019-01-24 20:35:32 UTC  
> Closed at: 2019-01-24 20:35:31 UTC  
> Url: https://github.com/boostorg/histogram/issues/142  

from #103:  
Sometimes the compilation errors are nonsensical. For example:  
  
// Forgot to use make_static_histogram_with()  
auto h = make_static_histogram(array_storage,  
axis::regular<>(10, 0, 1));  
  
triggers an incomprehensible static_assert plus an error about a missing  
.shape() function.

---

## Comment 1

> Username: HDembinski  
> Created at: 2019-01-24 20:35:31 UTC  
> Url: https://github.com/boostorg/histogram/issues/142#issuecomment-457346569  

fixed in develop
