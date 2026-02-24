# #48 - Lexical_cast throws an error converting string "08" to int128_t [Closed]

> Username: ProfChris  
> Created at: 2021-06-16 11:27:25 UTC  
> Updated at: 2024-02-13 08:49:31 UTC  
> Closed at: 2024-02-13 08:49:31 UTC  
> Url: https://github.com/boostorg/lexical_cast/issues/48  

Using boost library 1_76_0 the following C++ program throws an exception.    
#include <iostream>  
  
#include <boost/multiprecision/cpp_int.hpp>  
#include <boost/lexical_cast.hpp>  
  
using namespace std;  
using namespace boost::multiprecision;  
  
int main()  
{  
		int128_t r_number;		  
		string reverse_number = "08";  
		r_number = boost::lexical_cast<int128_t>(reverse_number);   
		cout << r_number;  
}  
  
other strings i.e "02", "03" etc convert successfully.   
I solved my problem by removing leading zeros prior to conversion.

---

## Comment 1

> Username: pdimov  
> Created at: 2022-02-08 23:37:36 UTC  
> Url: https://github.com/boostorg/lexical_cast/issues/48#issuecomment-1033170762  

The reason this happens is that numbers starting with 0 are interpreted as octal by Multiprecision's `operator>>`. So, for instance, the string `012` is interpreted as 10. `08` is rejected because it's not a valid octal number. See https://godbolt.org/z/1zc3qdn3c.  
  
Since this behavior comes from Multiprecision's `operator>>`, it's not a `lexical_cast` issue and should be reported to Multiprecision in case the behavior isn't by design.
