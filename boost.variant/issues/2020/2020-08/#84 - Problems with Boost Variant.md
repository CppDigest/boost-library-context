# #84 - Problems with Boost Variant [Closed]

> Username: pschaeffer  
> Created at: 2020-08-17 23:22:16 UTC  
> Updated at: 2020-08-18 13:59:02 UTC  
> Closed at: 2020-08-18 08:52:25 UTC  
> Url: https://github.com/boostorg/variant/issues/84  

The program below does not compile. It is quite simple and you would think that it would.  
  
#include "boost/variant.hpp"  
class PROgOperTypes {  
public:  
  enum operTypesEnum : int8_t {  
    anyOk       
  };  
  PROgOperTypes(operTypesEnum type) : enumValue(type) {}  
  operTypesEnum  enumValue;  
};  
int main() {  
  typedef boost::variant<double, int64_t>  PROgValueVariant;  
  PROgValueVariant dummyVar = PROgOperTypes::anyOk;  
}  
  
What am I doing wrong? Note that adding PROgOperTypes to the variant does not fix the problem. I should say that I am using Boost 1_72 so I should be reasonably up-to-date.

---

## Comment 1

> Username: pschaeffer  
> Created at: 2020-08-17 23:23:05 UTC  
> Url: https://github.com/boostorg/variant/issues/84#issuecomment-675163207  

Thank you folks

---

## Comment 2

> Username: apolukhin  
> Created at: 2020-08-18 08:52:25 UTC  
> Url: https://github.com/boostorg/variant/issues/84#issuecomment-675351157  

That is an intended behavior, `std::variant` does not compile in the same manner: https://godbolt.org/z/nT9qcc  
  
Adding an explicit `static_cast` to the sample fixes the compilation: https://godbolt.org/z/KMz1Pj

---

## Comment 3

> Username: pschaeffer  
> Created at: 2020-08-18 13:59:02 UTC  
> Url: https://github.com/boostorg/variant/issues/84#issuecomment-675495226  

Mr. Apolukhin,  
Wow. That fixed it.   
Thank you  
Peter Schaeffer
