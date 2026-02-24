# #57 - regex \R with flag no_escape_in_lists does not match \v and \n [Closed]

> Username: youcefl  
> Created at: 2018-02-05 14:33:19 UTC  
> Updated at: 2018-07-22 10:27:08 UTC  
> Closed at: 2018-07-22 10:26:45 UTC  
> Url: https://github.com/boostorg/regex/issues/57  

I think this is a regression. The following code  
```  
#include <iostream>  
#include <boost/regex.hpp>  
  
int main(int argc, char** argv)  
{  
	auto backslashR = boost::regex("\\R", boost::regex_constants::no_escape_in_lists);  
  
	std::cout << std::boolalpha;  
	std::cout << "regex='\\R', text='\\r', match found: " << boost::regex_search("\r", backslashR) << std::endl;  
	std::cout << "regex='\\R', text='\\n', match found: " << boost::regex_search("\n", backslashR) << std::endl;  
	std::cout << "regex='\\R', text='\\v', match found: " << boost::regex_search("\v", backslashR) << std::endl;  
  
	return 0;  
}  
  
```  
outputs  
```  
regex='\R', text='\r', match found: true  
regex='\R', text='\n', match found: true  
regex='\R', text='\v', match found: true  
```  
using boost regex v1.55 and v1.64. Using version 1.66 I get this:  
```  
regex='\R', text='\r', match found: true  
regex='\R', text='\n', match found: false  
regex='\R', text='\v', match found: false  
  
```

---

## Comment 1

> Username: jzmaddock  
> Created at: 2018-07-22 10:27:08 UTC  
> Url: https://github.com/boostorg/regex/issues/57#issuecomment-406855940  

Confirmed, fixed in develop.
