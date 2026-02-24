# #279 Use eventfd() function with uClibc [Closed]

> Username: neheb  
> Created at: 2019-09-06 02:41:21 UTC  
> Updated at: 2020-12-14 00:11:37 UTC  
> Closed at: 2020-12-14 00:11:33 UTC  
> Url: https://github.com/boostorg/asio/pull/279  

The Boost eventfd code either directly makes the eventfd system call  
using __NR_eventfd (when __GLIBC_MINOR is less than 8), or otherwise  
uses the eventfd() function provided by the C library.  
  
However, since uClibc pretends to be glibc 2.2, the Boost eventfd code  
directly uses the system call. While it works fine on most  
architectures, it doesn't on ARC since __NR_eventfd is not defined on  
this architecture. However, eventfd() is properly implemented.  
  
So, this patch adjusts the logic used by Boost to consider uClibc as a  
C library providing the eventfd() function.  
  
Signed-off-by: Thomas Petazzoni <thomas.petazzoni@free-electrons.com>  
Signed-off-by: Rosen Penev <rosenp@gmail.com>

---
