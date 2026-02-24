# #32 - wave can't process includes with unicode characters in paths on windows [Closed]

> Username: rlenhardt  
> Created at: 2018-09-28 10:47:50 UTC  
> Updated at: 2018-10-18 07:24:46 UTC  
> Closed at: 2018-10-18 07:24:46 UTC  
> Url: https://github.com/boostorg/wave/issues/32  

Suppose i have a directory ßµ containing header.h with content  
  
    const char* file() { return __FILE__; }  
  
and source.cpp with content  
  
    #include <header.h>  
    #include <iostream>  
    int main() { std::cout << file() << std::endl; }  
  
Now on windows calling the wave driver tool in the directory ßµ like this  
`wave -I. source.cpp`  
fails to find the header, same when calling wave from the parent directory of ßµ:  
`wave -Ißµ ßµ\source.cpp`  
Moving source.cpp to the parent directory will fail when trying to open the included header.  
Changing the include in source.cpp to `#include <ßµ/header.h>` and calling wave with  
`wave -I. source.cpp`  
also fails to include the header.  
  
All this stems from wave using 8 bit strings for filenames and include paths internally and using standard streams to open and read the actual files. The conversion to boost::filesystem::path and back to string seems to mess up the path encoding at some place. This seems to be a problem for many years, see https://svn.boost.org/trac10/ticket/6970.  
  
A quick fix would be to use UTF-8 as path encoding in wave by setting up boost::filesystem::path to recognize UTF-8, this can be done at that start of wave or outside of wave like this (taken from [here](http://boost.2283326.n4.nabble.com/boost-filesystem-path-as-utf-8-tp4320098p4322460.html)):  
  
    #include <boost/filesystem/detail/utf8_codecvt_facet.hpp>  
    ...  
    std::locale global_loc = std::locale();  
    std::locale loc(global_loc, new  
    boost::filesystem::detail::utf8_codecvt_facet);  
    boost::filesystem::path::imbue(loc);   
  
To make this work, all standard streams used to open files have to be switched to the boost filesystem stream wrappers. Then the 8 bit string filename input of the stream open method will be taken as UTF-8 encoded and opened correctly.  
  
I have tried that this approach works. While you could easily adapt the Input Policy of wave from outside of wave it would be nice if wave and the wave driver would use the boost filesystem wrappers internally, so changing the boost filesystem locale like mentioned above just works.  
  
If this sounds like a good idea to include in the wave lib i can set up a pull request.  
  
Thanks

---

## Comment 1

> Username: hkaiser  
> Created at: 2018-09-28 11:59:58 UTC  
> Url: https://github.com/boostorg/wave/issues/32#issuecomment-425413243  

@rlenhardt I'd appreciate a corresponding PR! Thanks!

---

## Comment 2

> Username: rlenhardt  
> Created at: 2018-10-18 07:24:46 UTC  
> Url: https://github.com/boostorg/wave/issues/32#issuecomment-430903209  

I think we can close this issue, i'm happy with the changes merged into wave. Thanks a lot!
