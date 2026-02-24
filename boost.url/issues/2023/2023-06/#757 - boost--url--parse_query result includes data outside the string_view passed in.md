# #757 - boost::url::parse_query result includes data outside the string_view passed in [Closed]

> Username: adamhpeterson  
> Created at: 2023-06-20 18:09:04 UTC  
> Updated at: 2023-07-18 18:34:25 UTC  
> Closed at: 2023-07-18 18:34:24 UTC  
> Url: https://github.com/boostorg/url/issues/757  

url::parse_query() seems to parse query values beyond the data passed into the function's string_view argument.  I see this using Boost 1.82.  
  
Example reproduction:  
```  
#include <boost/url/parse.hpp>  
#include <boost/url/parse_query.hpp>  
#include <iostream>  
#include <string>  
#include <string_view>  
  
int main() {  
  auto data = std::string("abc=def&ghi=jkl&mno=pqr");  
  auto view = std::string_view( data.data(), data.size() - 2 );  
  auto fp = boost::urls::parse_query( view );  
  for ( auto &&p : fp.value() ) {  
    std::cout << "(" << p.key << ")(" << p.value << ")\n";  
  }  
}  
  
// Expected output:  
//  (abc)(def)  
//  (ghi)(jkl)  
//  (mno)(p)  
// Actual output:  
//  (abc)(def)  
//  (ghi)(jkl)  
//  (mno)(pqr)  
```  
  
Notice that although the string_view was shortened by 2 characters from the full string before it was passed into `parse_query()`, the two characters are still included in the result of the parse.
