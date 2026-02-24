# #82 - Wrong result for classes with user-defined conversion [Closed]

> Username: ashcheglov  
> Created at: 2025-05-13 03:04:51 UTC  
> Updated at: 2025-05-13 16:32:22 UTC  
> Closed at: 2025-05-13 16:32:22 UTC  
> Url: https://github.com/boostorg/lexical_cast/issues/82  

I upgraded the boost library to 1.88 and ran into an error with the wrong conversion ([godbolt link](https://godbolt.org/z/rjEnss969)).  
  
```c++  
#include <string>  
#include <cassert>  
#include <boost/lexical_cast.hpp>  
   
struct Value  
{  
  Value(int32_t x) : x(x) {}  
 operator double() const { return static_cast<double>(x); }  
  
  int32_t x;  
};  
   
int main()  
{  
  Value value(142);  
  auto s = boost::lexical_cast<std::string>(value);  
  assert(s == "142");  
}  
```  
It seems that the buffer in `boost::detail::optimized_src_stream::optimized_src_stream` is way too short to accommodate the full value. `boost::detail::lcast_src_length<>` metafunction falls back to the default length 1 because `boost::is_float<Value>` returns false and no specialization found
