# #50 - Exception thrown by safe::operator~() for Stored types smaller than `int` [Closed]

> Username: nmusolino  
> Created at: 2017-03-23 02:20:06 UTC  
> Updated at: 2018-09-23 22:26:36 UTC  
> Closed at: 2018-08-10 20:38:48 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/50  

As detailed in Issue #49, the static_assert in `safe::operator~()` is missing a logical `!`.  
  
BUT when I attempted to correct this and write a simple unit test, I observed surprising behavior.  The simplest reproduction is:  
  
```C++  
#include "../include/safe_integer.hpp"  
  
#include <cstdint>  
  
// Note: requires trivial fix for static_assert problem described in Issue #49.  
int main(){  
    const boost::numeric::safe<std::uint8_t> val(1);  
    ~val;  
}  
```  
Result:    
```  
libc++abi.dylib: terminating with uncaught exception of type std::domain_error: Value out of range for this safe type  
```  
  
I argue that this is surprising behavior: I expect that bitwise not should always succeed and return a result in range.  (I think the `constexpr` designation reinforces this expectation.)  
  
The reason for this error is the [implementation of operator~()](https://github.com/robertramey/safe_numerics/blob/master/include/safe_base.hpp#L261):  
```  
    constexpr safe_base operator~() const {  
        static_assert(  
            !std::numeric_limits<Stored>::is_signed,  
            "Bitwise inversion of signed value is an error"  
        );  
        return ~(m_t);  
    }  
```  
  
The expression `~(m_t)` results in [type promotion](http://stackoverflow.com/questions/30473958/what-is-going-on-with-bitwise-operators-and-integer-promotion), and is of type `int` when `Stored` is `std::uint8_t`.  When the `safe_base<std::uint8_t>` is constructed from the large `int` value (through the implicit conversion to the return type), an exception is thrown.  
  
This could be fixed with an explicit cast to `Stored` when that type is smaller than int, but I'm not sure that's in the spirit of this library.

---

## Comment 1

> Username: nmusolino  
> Created at: 2017-03-23 02:24:51 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/50#issuecomment-288598500  

See also this discussion of an earlier pull request here: [Wrong check in operator~ #23](https://github.com/robertramey/safe_numerics/pull/23).

---

## Comment 2

> Username: robertramey  
> Created at: 2018-08-10 20:38:48 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/50#issuecomment-412199482  

I think I've come to agree with you.  The current code does not invoke this exception.
