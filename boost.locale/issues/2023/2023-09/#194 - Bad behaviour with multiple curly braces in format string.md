# #194 - Bad behaviour with multiple curly braces in format string [Closed]

> Username: Cogitri  
> Created at: 2023-09-19 06:31:08 UTC  
> Updated at: 2023-09-20 14:30:35 UTC  
> Closed at: 2023-09-20 14:17:26 UTC  
> Url: https://github.com/boostorg/locale/issues/194  

Hello,  
  
I believe #158 introduced a bug when a to-be-formatted string contains multiple curly braces:  
  
Before 1.83.0, the following snippet:  
  
```  
#include <boost/locale.hpp>  
#include <boost/format.hpp>  
#include <iostream>  
  
int main() {  
	std::cout << boost::locale::format("Test string {{{1}}}") % "num";  
  
	return 0;  
}  
```  
  
Returned:  
  
```  
Test String {{num}}  
```  
  
After 1.83.0 it returns:  
  
```  
Test string {{num}  
```  
  
I admit this is sort of an edge case, but I feel like the previous behaviour was more correct.

---

## Comment 1

> Username: Flamefire  
> Created at: 2023-09-19 08:26:01 UTC  
> Updated at: 2023-09-19 08:27:15 UTC  
> Url: https://github.com/boostorg/locale/issues/194#issuecomment-1725050131  

I'm not sure how that specific PR could cause that behavior, but I agree that neither version produces what I'd expect.  
  
Edit: it is rather caused by a [refactoring](https://github.com/boostorg/locale/commit/d80b3b6ebcc91f14db068ca8838beec4df9089c9) which misses skipping the escaped opening brace  
  
`boost::locale::format("Test string {{{1}}}") % "num"` should IMO yield `Test string {num}` as per the documentation:  
  
> You can include a literal '{' and '}' by inserting double "{{" or "}}" into the text.  
  
--> There is an escaped brace, a format-placeholder and another escaped brace

---

## Comment 2

> Username: Cogitri  
> Created at: 2023-09-20 14:30:34 UTC  
> Url: https://github.com/boostorg/locale/issues/194#issuecomment-1727850773  

Thanks for the fix!
