# #66 - Newer versions of OpenBSD are not detected [Closed]

> Username: jeking3  
> Created at: 2017-11-07 17:09:52 UTC  
> Updated at: 2017-11-09 05:01:36 UTC  
> Closed at: 2017-11-09 05:01:36 UTC  
> Url: https://github.com/boostorg/predef/issues/66  

`include/boost/predef/os/open.h` detects OpenBSD up to 4.9, however manual pages show there are later versions, at least 5.6, and the web site says 6.2:  
  
http://man.openbsd.org/OpenBSD-current/man2/getentropy.2  
https://www.openbsd.org/  
  
The the same pattern found in detection follows suit, one could pre-populate this list to help future-proof it, or perhaps use some other technique to detect.  
  
As an aside, the OpenBSD team should define the MAJOR and MINOR versions as separate items so it can be used programmatically!  Ugh.
