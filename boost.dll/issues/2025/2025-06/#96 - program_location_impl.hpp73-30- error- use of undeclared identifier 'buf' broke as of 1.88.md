# #96 - program_location_impl.hpp73:30: error: use of undeclared identifier 'buf'  broke as of 1.88 [Closed]

> Username: agokhale  
> Created at: 2025-06-10 12:45:24 UTC  
> Updated at: 2025-06-11 06:59:26 UTC  
> Closed at: 2025-06-11 06:59:26 UTC  
> Url: https://github.com/boostorg/dll/issues/96  

Found during a freebsd ports build break:  
https://pkg-status.freebsd.org/gohan06/data/142amd64-default-foo/2025-06-05_20h23m02s/logs/PrusaSlicer-2.9.2_1.log  
  
This line can't build as of 1.88.0 :   
https://github.com/boostorg/dll/blame/c30df76b0492827c6e3b6177c733f45987094426/include/boost/dll/detail/posix/program_location_impl.hpp#L73  
  
`73: size_t size = sizeof(buf);`  
  
This commit  ( blamed from 1.87 -> 1.88 )  appears to delete the definition of 'buf'.  
https://github.com/boostorg/dll/commit/9d452d14594749772f2cf601efa37bf9c8f56717#r159658911  
  
Perhaps @apolukhin  can help.
