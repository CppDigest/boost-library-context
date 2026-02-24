# #552 - x3::variant operator << prints empty [Closed]

> Username: allopislozano  
> Created at: 2019-11-18 16:52:34 UTC  
> Updated at: 2019-11-19 21:37:20 UTC  
> Closed at: 2019-11-19 21:37:20 UTC  
> Url: https://github.com/boostorg/spirit/issues/552  

Hi there, the operator << of x3::variant always prints an empty string. Is this a bug?  
  
https://wandbox.org/permlink/qS2XErm6hbi2ZLt9  
  
```  
#include<vector>  
#include<string>  
#include <utility>  
#include <iostream>  
#define BOOST_SPIRIT_X3_DEBUG  
#include <boost/spirit/home/x3.hpp>  
#include <boost/spirit/home/x3/support/ast/variant.hpp>  
  
using std::vector;  
using std::string;  
namespace x3 = boost::spirit::x3;  
int main()  
{  
   using V = boost::variant<string,int>;  
  
   V v = V(string("hello"));  
  
   std::cout << "V:" << v << std::endl;  
      
   using V2 = x3::variant<string,int>;  
   V2 v2 = V2(string("hello"));  
  
   std::cout << "V2:" << v2 << std::endl;  
  
}  
```

---

## Comment 1

> Username: Kojoley  
> Created at: 2019-11-19 18:53:55 UTC  
> Url: https://github.com/boostorg/spirit/issues/552#issuecomment-555654420  

This compiles by accident, `x3::variant` does not have streaming operators.
