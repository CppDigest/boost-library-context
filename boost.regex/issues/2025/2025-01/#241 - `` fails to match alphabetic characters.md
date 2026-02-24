# #241 - `[\W\D]` fails to match alphabetic characters [Open]

> Username: muellerj2  
> Created at: 2025-01-20 12:35:33 UTC  
> Updated at: 2025-01-20 12:35:33 UTC  
> Url: https://github.com/boostorg/regex/issues/241  

The (ECMAScript) regular expression `[\W\D]` describes a character class that matches the union of (a) all non-alphanumeric characters and (b) all non-digits. So effectively, should be equivalent to `[\D]` and thus match all non-digits. However, Boost.Regex actually matches only non-alphanumeric characters.  
  
Test case:  
```  
#include <iostream>  
#include <boost/regex.hpp>  
  
using namespace boost;  
  
int main()  
{  
    regex re(R"([\W\D])");  
    std::cout << "matches alphabetic: " << regex_match("a", re) << '\n'  
         << "matches digit: " << regex_match("0", re) << '\n'   
         << "matches non-alphanumeric: " << regex_match(".", re);  
      
    return 0;  
}  
```  
https://godbolt.org/z/jPf79j5nr  
  
This prints:  
```  
matches alphabetic: 0  
matches digit: 0  
matches non-alphanumeric: 1  
```  
  
But it should print:  
```  
matches alphabetic: 1  
matches digit: 0  
matches non-alphanumeric: 1  
```  
  
I think the problem lies here:  
https://github.com/boostorg/regex/blob/35937503ca4fff3434a3a5f0d76258231eb442cf/include/boost/regex/v5/basic_regex_creator.hpp#L106-L110  
  
The negated character classes are bitwise or'ed, but De Morgan's law says that `(not w) or (not d) = not (w and d)`, so the bit masks should really be bitwise and'ed.   
  
But bitwise and'ing would be problematic as well, because no requirement is placed on traits classes that and'ing the character class bit masks corresponds to the intersection of the character classes. I guess and'ing will probably still work for traits classes provided by Boost.Regex (although I haven't checked that), but it's not guaranteed to do the right thing for user-provided traits classes.
