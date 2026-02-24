# #92 - variant not supporting __int128 data type [Closed]

> Username: anilmuthigi  
> Created at: 2021-04-08 18:40:30 UTC  
> Updated at: 2021-04-09 05:56:34 UTC  
> Closed at: 2021-04-09 05:56:25 UTC  
> Url: https://github.com/boostorg/variant/issues/92  

I tried running the code given below, but for some reason it wouldn't compile. If you replace the __int128 data type with the int data type for just the variant variable, the code works just fine. Hence, I feel like variant does not support __int128 data type  
```cpp  
#include <boost/variant.hpp>  
#include <string>  
#include <iostream>  
std::ostream& operator<<(std::ostream& o, const __int128& x)   
{  
	if (x == std::numeric_limits<__int128>::min())   
        	return o << "-170141183460469231731687303715884105728";   
	if (x < 0)   
	        return o << "-" << -x;   
	if (x < 10)   
	        return o << (char)(x + '0');   
	return o << x / 10 << (char)(x % 10 + '0');   
   
}  
int main()  
{  
      boost::variant<__int128, char, std::string> v;  
      v = 56;  
      v = 'Y';  
      __int128 d=12;  
      std::cout <<d << '\n';  
      std::cout << v << '\n';   
      v = "Name";  
      std::cout << v << '\n';  
}  
```

---

## Comment 1

> Username: apolukhin  
> Created at: 2021-04-09 05:56:25 UTC  
> Updated at: 2021-04-09 05:56:34 UTC  
> Url: https://github.com/boostorg/variant/issues/92#issuecomment-816429479  

There are two issues in your code. Fixing those makes the sample compile https://godbolt.org/z/E7fnGMq6q   
```cpp  
#include <boost/variant.hpp>  
#include <string>  
#include <iostream>  
  
namespace std {  // required for ADL to find the operator  
  
// Not a very good idea! You'd better write a separate function for   
// outputting the result, rather than adding an operator << overload.  
std::ostream& operator<<(std::ostream& o, const __int128& x) {  
	if (x == std::numeric_limits<__int128>::min())   
        	return o << "-170141183460469231731687303715884105728";   
	if (x < 0)   
	        return o << "-" << -x;   
	if (x < 10)   
	        return o << (char)(x + '0');   
	return o << x / 10 << (char)(x % 10 + '0');   
    return o;  
}  
  
} // namespace std  
  
int main()  
{  
      boost::variant<__int128, char, std::string> v;  
      v = __int128(56); // converting from int is ambigious, you have to be explicit here   
      v = 'Y';  
      __int128 d=12;  
      std::cout <<d << '\n';  
      std::cout << v << '\n';   
      v = "Name";  
      std::cout << v << '\n';  
}  
```
