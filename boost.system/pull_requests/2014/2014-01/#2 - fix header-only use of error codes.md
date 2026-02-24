# #2 fix header-only use of error codes [Merged]

> Username: timblechmann  
> Created at: 2014-01-04 11:39:10 UTC  
> Updated at: 2014-01-06 22:43:05 UTC  
> Merged at: 2014-01-06 22:43:05 UTC  
> Closed at: 2014-01-06 22:43:05 UTC  
> Url: https://github.com/boostorg/system/pull/2  

BOOST_ERROR_CODE_HEADER_ONLY was not working when the headers are separated  
from the sources (e.g. when installing the headers to fhs-style /usr/include  
or the like). to avoid this, we move the implementation to an .ipp file  
  
Signed-off-by: Tim Blechmann tim@klingt.org

---
