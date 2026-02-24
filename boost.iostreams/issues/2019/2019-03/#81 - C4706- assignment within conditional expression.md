# #81 - C4706: assignment within conditional expression [Open]

> Username: reid-p  
> Created at: 2019-03-18 08:42:59 UTC  
> Updated at: 2019-03-18 08:42:59 UTC  
> Url: https://github.com/boostorg/iostreams/issues/81  

boost\iostreams\filter\zlib.hpp(392): warning C4706: assignment within conditional expression  
  
This warning is seen from MSVC 15.9.8 / 15.9.9 during linking of a release build.  
Which is odd. Debug builds are fine.  
  
Offending code is:  
    return !(eof_ = result == zlib::stream_end);
