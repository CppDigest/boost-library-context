# #56 - boost::iostreams::copy - sink - ENOSPC (No space left on device) error handling [Open]

> Username: codemedic  
> Created at: 2018-03-12 18:50:49 UTC  
> Updated at: 2018-03-12 18:52:04 UTC  
> Url: https://github.com/boostorg/iostreams/issues/56  

Copying the issue reported on legacy Trac system  
https://svn.boost.org/trac10/ticket/9478  
  
Platform: GCC 7 on Linux with boost v1.66 (originally reported on older version; confirmed by re-testing)  
In the code fragment below, is there a way to handle ENOSPC?  
  
```  
#include <fstream>  
#include <iostream>  
#include <boost/iostreams/filtering_streambuf.hpp>  
#include <boost/iostreams/copy.hpp>  
#include <boost/iostreams/filter/bzip2.hpp>  
  
// open input file stream of the bzip2 file  
std::ifstream ifs("file.bz2");  
  
// open output stream to the "full" device  
// full device is a "utility-device" to check how applications handle ENOSPC  
// more details in "man full"  
std::ofstream ofs("/dev/full");  
  
// Setup the iostreams filter  
boost::iostreams::filtering_streambuf<boost::iostreams::output> filters;  
filters.push(boost::iostreams::bzip2_decompressor());  
filters.push(ofs);  
  
// "run" the filter  
boost::iostreams::copy(ifs, filters);  
```  
If I do strace of the compiled binary, the code seem to infinitely call writev() with the same data and returns ENOSPC error.  
```  
writev(4, [{NULL, 0}, {"DATA DATA "..., 4096}], 2) = -1 ENOSPC (No space left on device)  
```  
How can this error be handled or made thrown as an error from `boost::iostreams::copy()`  
  
Is it possible to set appropriate exceptions() on the ofstream object? I tried `ofs.exceptions(std::ios::badbit | std::ios::failbit)` but it didn't make any difference.  
  
More detailed discussion on SO: ​http://stackoverflow.com/q/20414577/83005  
  
A shorter code to reproduce the issue.  
  
```  
#include <boost/iostreams/filtering_streambuf.hpp>  
#include <boost/iostreams/copy.hpp>  
#include <boost/iostreams/device/file.hpp>  
  
int main()  
{  
    using namespace boost::iostreams;  
    file_sink   ofs("/dev/full");  
    file_source ifs("/dev/zero");  
  
    filtering_streambuf<output> filters(ofs);  
    copy(ifs, filters);  
}  
```
