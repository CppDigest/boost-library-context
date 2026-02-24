# #126 - Forward seekable compressed streams [Open]

> Username: maxnoe  
> Created at: 2021-02-06 11:41:24 UTC  
> Updated at: 2022-02-01 21:09:55 UTC  
> Url: https://github.com/boostorg/iostreams/issues/126  

Using `boost::iostreams::filtering_istream` together with one of the decompression filters, e.g. `boost::iostreams::gzip_decompressor` results in `tellg` returning -1 and setting the bad bid and `seekg` not working.  
  
However, at least forward seeking and telling the number read bytes would be possible and would make working with these streams much easier for certain use cases (e.g. reading custom binary files compressed with these methods).  
  
i.e. this:  
  
```  
  
#include <boost/iostreams/filtering_stream.hpp>  
#include <boost/iostreams/filter/gzip.hpp>  
#include <boost/iostreams/device/file.hpp>  
  
  
namespace io = boost::iostreams;  
int main() {  
    io::filtering_istream stream;  
    stream.push(io::gzip_decompressor());  
    stream.push(io::file_source("test.gzip"));  
      
    std::cout << stream.tellg() << std::endl; // should print 0, prints -1  
    stream.seekg(1000); // skip 1000 bytes  
    std::cout << stream.tellg() << std::endl; // should print 1000, prints -1  
      
    return 0;  
}  
```  
See for example `gzseek`: https://refspecs.linuxbase.org/LSB_3.0.0/LSB-Core-generic/LSB-Core-generic/zlib-gzseek-1.html  
  
and `gztell`: https://refspecs.linuxbase.org/LSB_3.0.0/LSB-Core-generic/LSB-Core-generic/zlib-gztell-1.html

---

## Comment 1

> Username: rdoeffinger  
> Created at: 2022-02-01 21:09:55 UTC  
> Url: https://github.com/boostorg/iostreams/issues/126#issuecomment-1027290391  

Not sure what happens for tellg, but I am not sure if the seekg behaviour is not the best possible.  
Because for cases where seeking is not strictly possible since it will always involve reading all data, it might make sense to require use of stream.ignore(). That is assuming stream.ignore() is working, I have not tested it.
