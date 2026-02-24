# #242 - Issue with string::rfind [Closed]

> Username: gashtio  
> Created at: 2023-04-18 09:34:30 UTC  
> Updated at: 2023-04-18 20:51:19 UTC  
> Closed at: 2023-04-18 20:50:37 UTC  
> Url: https://github.com/boostorg/container/issues/242  

There seems to be a problem with an old commit that was fixing GCC warnings but I guess a typo was made and now `string::rfind` doesn't work correctly. The commit I found is this: https://github.com/boostorg/container/commit/dad2cb2d02aeccbf2d47eb0e18d73e52b9c51991#diff-aa8e68362c274ac9daa4a59b84b905bfc55747cd6d52c0ec08d3fa1d1350b9beR2442  
  
The previous calculation took the minimum of the haystack length minus the length of the needle and the offset, which is `npos` by default and then added the needle length separately. Now, the addition is made to the offset argument, which could overflow and the minimum is no longer correct.  
  
Here's a minimal example that demonstrates the issue:  
  
```  
#include <boost/container/string.hpp>  
#include <string>  
#include <iterator>  
#include <iostream>  
  
constexpr const char* HAYSTACK = "cubic_bezier(0,1,0,0)";  
constexpr const char* NEEDLE = ")";  
  
int main() {  
	boost::container::string boost_str(HAYSTACK);  
	auto boost_rfind_pos = boost_str.rfind(NEEDLE);  
  
	std::string std_str(HAYSTACK);  
	auto std_rfind_pos = std_str.rfind(NEEDLE);  
  
	std::cout << "BOOST position: " << boost_rfind_pos <<  
		"\nSTD position: " << std_rfind_pos << std::endl;  
  
	return 0;  
}  
```  
  
Output is:  
```  
BOOST position: 18446744073709551615  
STD position: 20  
```  
  
The fix should be trivial, just separate the addition of `n` from the minimum function, as it was before.

---

## Comment 1

> Username: igaztanaga  
> Created at: 2023-04-18 20:51:18 UTC  
> Url: https://github.com/boostorg/container/issues/242#issuecomment-1513779496  

Many thanks for the report!
