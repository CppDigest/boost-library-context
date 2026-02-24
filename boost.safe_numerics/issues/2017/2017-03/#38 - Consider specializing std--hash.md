# #38 - Consider specializing std::hash [Closed]

> Username: badair  
> Created at: 2017-03-12 00:42:18 UTC  
> Updated at: 2018-09-23 22:26:36 UTC  
> Closed at: 2018-08-10 22:00:09 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/38  

...for use in STL unordered containers

---

## Comment 1

> Username: robertramey  
> Created at: 2017-04-09 20:58:05 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/38#issuecomment-292812518  

Please expand upon this.  I'm can't see where this fits into the safe numerics library

---

## Comment 2

> Username: badair  
> Created at: 2017-04-22 16:22:25 UTC  
> Updated at: 2017-04-22 16:25:05 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/38#issuecomment-296384291  

If `safe<int>` is really supposed to be a drop-in replacement for `int`, then I would expect this code to compile:  
  
```  
#include <string>  
#include <unordered_map>  
#include "safe_integer.hpp"  
  
int main(){  
    auto foo = std::unordered_map<boost::numeric::safe<int>, std::string>{};  
    foo[42] = "hello, world!";  
}  
```  
Instead, I get this:  
  
`error: no match for call to ‘(const std::hash<boost::numeric::safe_base<int, -2147483648, 2147483647, boost::numeric::native, boost::numeric::throw_exception> >) (const boost::numeric::safe_base<int, -2147483648, 2147483647, boost::numeric::native, boost::numeric::throw_exception>&)’`  
  
Specializing `std::hash` would solve this problem.

---

## Comment 3

> Username: robertramey  
> Created at: 2018-08-10 21:34:37 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/38#issuecomment-412212720  

Hmm - as an experiment I tested the following with my clang compiler on mac osx:  
```  
#include <string>  
#include <unordered_map>  
#include "safe_integer.hpp"  
  
#include <functional>  
using x = const std::hash<boost::numeric::safe<int>>;  
  
int main(){  
    auto foo = std::unordered_map<x, std::string>{};  
    foo[42] = "hello, world!";  
}  
```  
  
and got an error message which just displays that I don't know anything.   
  
/Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/include/c++/v1/functional:2480:5: Static_assert failed "This hash only works for enumeration types"  
  
In any case, it's too special purpose for my taste.  It would make the library work but in a way that hides the operation from the user - turning it into "magic".  Generally I don't like this.  
  
But if you can make this work as an example, I'd be happy to added to examples and the docs.  There's a section related to composition with other libraries where it would fit nicely.  Thanks for helping me out with this.

---

## Comment 4

> Username: robertramey  
> Created at: 2018-08-10 21:55:04 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/38#issuecomment-412216705  

OK - this was bugging me so I invested a little more time in it.  
  
```  
#include <string>  
#include <unordered_map>  
#include "safe_integer.hpp"  
  
#include <functional> // hash  
  
template<typename T>  
struct safe_hash {  
    size_t operator()(boost::numeric::safe<T> const& t) const  {  
        return std::hash<T>()(t);  
    }  
};  
  
int main(){  
    auto foo = std::unordered_map<  
        boost::numeric::safe<int>,  
        std::string,  
        safe_hash<int>  
    >{};  
    foo[boost::numeric::safe<int>(42)] = "hello, world!";  
    foo[42] = "hello, world!";  
}  
```  
and this seems to work as advertised.

---

## Comment 5

> Username: robertramey  
> Created at: 2018-08-10 22:00:09 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/38#issuecomment-412217704  

also the following seems to work well:  
```  
#include <string>  
#include <unordered_map>  
#include "safe_integer.hpp"  
  
#include <functional> // hash  
  
template<typename T>  
struct safe_hash {  
    size_t operator()(boost::numeric::safe<T> const& t) const  {  
        return std::hash<T>()(t);  
    }  
};  
  
int main(){  
    auto foo = std::unordered_map<int, std::string>{};  
    foo[boost::numeric::safe<int>(42)] = "hello, world!";  
}  
```  
It just converts safe types to int types for the hashing.  If we're comfortable presuming that the std:: function has no bugs, this avoid inserting code checking code into the implementation of hash function calculation.    
  
Basically I'm inclined to think this should just be a small ehancement to the documentation
