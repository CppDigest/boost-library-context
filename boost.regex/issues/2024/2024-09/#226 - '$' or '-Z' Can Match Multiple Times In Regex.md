# #226 - '$' or '\Z' Can Match Multiple Times In Regex [Closed]

> Username: nllyy  
> Created at: 2024-09-18 11:52:14 UTC  
> Updated at: 2024-09-22 16:22:37 UTC  
> Closed at: 2024-09-22 16:22:36 UTC  
> Url: https://github.com/boostorg/regex/issues/226  

```C++  
#include <iostream>  
#include <boost/regex.hpp>  
using namespace std;  
using namespace boost;  
int main()  
{  
	string str = "A";  
	regex r(R"(A?$)");	//or A?\Z  
	str = regex_replace(str, r, "E");  
	cout << str;  
	return 0;  
}  
```  
It will output "EE", but when I use std::regex, the output is "E".  
It seems that “$” can be matched multiple times in boost, is this by design? Or is it a bug?

---

## Comment 1

> Username: jzmaddock  
> Created at: 2024-09-22 16:22:37 UTC  
> Url: https://github.com/boostorg/regex/issues/226#issuecomment-2366860202  

This is by design: the first match matches the "A", the second the null string after the "A".  This is the way Perl and all compatible matchers do it.  I'm not sure why the std isn't, IMO it should be ;)
