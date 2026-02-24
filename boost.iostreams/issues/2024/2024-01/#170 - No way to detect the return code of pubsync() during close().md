# #170 - No way to detect the return code of pubsync() during close() [Open]

> Username: ednolan  
> Created at: 2024-01-19 23:08:17 UTC  
> Updated at: 2024-01-19 23:08:17 UTC  
> Url: https://github.com/boostorg/iostreams/issues/170  

This program completes successfully despite the fact that it failed to write any data:  
  
```  
#include <boost/iostreams/filtering_stream.hpp>  
#include <boost/iostreams/filter/gzip.hpp>  
#include <fstream>  
  
int main() {  
  std::ofstream s{"/dev/full"};  
  boost::iostreams::filtering_ostream filtering{};  
  filtering.push(boost::iostreams::gzip_compressor{});  
  filtering.push(s);  
  filtering << "Hello, world!";  
  boost::iostreams::close(filtering);  
  // at this point, s.good() is true  
}  
```  
  
Debugging reveals that inside of `close()`, a call to `pubsync()` has indicated an error flushing out the data. This error is noticed by `boost::iostreams`'s `flush` implementation (`flush.hpp:65`):  
  
```  
template<>  
struct flush_device_impl<ostream_tag> {  
    template<typename T>  
    static bool flush(T& t)  
    { return t.rdbuf()->BOOST_IOSTREAMS_PUBSYNC() == 0; }  
};  
```  
  
But `flush`'s result is ignored inside of the `close()` implementation (`close.hpp:170`):  
  
```  
    template<typename T>  
    static void close(T& t, BOOST_IOS::openmode which)  
    {  
        if (which == BOOST_IOS::out)  
            iostreams::flush(t);  
    }  
```  
  
Since the compressing filters are not flushable, the only way to flush them is to close them; since this error is ignored on close, there appears to be no way in the API to discover this form of write failure when a compressing filter is in the chain.  
  
There should be a way to avoid ignoring this error. Maybe `close` should throw if the call to `iostreams::flush` fails.
