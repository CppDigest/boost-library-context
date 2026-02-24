# #382 - Html docs for Karma:Iterator Based Generator API reference dead SGI link for OutputIterator [Closed]

> Username: jdmairs  
> Created at: 2018-03-24 16:39:27 UTC  
> Updated at: 2018-09-29 19:58:36 UTC  
> Closed at: 2018-09-29 19:58:36 UTC  
> Url: https://github.com/boostorg/spirit/issues/382  

Instead of using a std::string with a back_inserter I'm trying to use a Microsoft Gudeline Support Library (GSL) string_span.  A string_span<> is basically a pointer and a length that allows safer buffer manipulation.

---

## Comment 1

> Username: Kojoley  
> Created at: 2018-09-29 19:58:36 UTC  
> Url: https://github.com/boostorg/spirit/issues/382#issuecomment-425671788  

Dead SGI link fixed in #372.  
  
  
To use a `gsl::string_span` with Karma you need to write an output iterator. Alternatively you can use `boost::iostreams::stream<boost::iostreams::array_sink>` to wrap the span into a stream and construct an iterator from it with `boost::spirit::ostream_iterator`.  
  
Example:  
```cpp  
#include <vector>  
#include <gsl/string_span>  
#include <boost/spirit/include/karma.hpp>  
#include <boost/iostreams/device/array.hpp>  
#include <boost/iostreams/stream.hpp>  
  
int main()  
{  
    std::vector<char> vec(5);  
    gsl::string_span<> span{vec};  
  
    boost::iostreams::stream<boost::iostreams::array_sink> stream(span.data(), span.size());  
    boost::spirit::ostream_iterator sink(stream);  
    boost::spirit::karma::generate(sink, ...);  
}  
```
