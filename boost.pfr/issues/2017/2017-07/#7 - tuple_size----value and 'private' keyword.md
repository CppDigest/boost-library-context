# #7 - tuple_size<>::value and 'private' keyword [Closed]

> Username: mdimura  
> Created at: 2017-07-25 16:44:15 UTC  
> Updated at: 2017-08-10 18:28:16 UTC  
> Closed at: 2017-08-10 18:28:16 UTC  
> Url: https://github.com/boostorg/pfr/issues/7  

```  
#include <iostream>  
#include "boost/pfr.hpp"  
  
class Test { // no ostream operator defined!  
private:  
    std::string s;  
    int i;  
    char c;  
public:  
    double d;  
    float f;  
};  
  
int main() {  
    std::cout << "Test has " << boost::pfr::tuple_size<Test>::value  
        << " fields" << "\n";  
}  
```  
This prints "Test has 1 fields". gcc 7.1.0, std=c++17. Is this intended behaviour? If I change `private:` to `public:`, I get "Test has 5 fields"

---

## Comment 1

> Username: mdimura  
> Created at: 2017-08-04 13:13:31 UTC  
> Url: https://github.com/boostorg/pfr/issues/7#issuecomment-320245475  

Answer my own question:  
  
> C++17 limitations: T must be aggregate initializable  
  
> An aggregate is an array or a class (Clause 9) with no user-provided constructors (12.1), no brace-or-equal-initializers for non-static data members (9.2), **no private or protected non-static data members** (Clause 11), no base classes (Clause 10), and no virtual functions (10.3).

---

## Comment 2

> Username: apolukhin  
> Created at: 2017-08-04 18:53:18 UTC  
> Url: https://github.com/boostorg/pfr/issues/7#issuecomment-320326855  

Actually this seems to be an error.  
  
I have to improve library internals to provide a compile time assert with a string like "Provided type is not an aggregate: private members are not allowed."

---

## Comment 3

> Username: apolukhin  
> Created at: 2017-08-10 18:28:16 UTC  
> Url: https://github.com/boostorg/pfr/issues/7#issuecomment-321635068  

Tried to do my best with private/protected. If you'll find a better way to deal with the problem, or some more missing `static_assert`s - please give me a hint!  
  
Great thanks for the error report and for the code snippet.
