# #111 Fix polynomial.hpp deprecated-copy warnings [Open]

> Username: jcstroud  
> Created at: 2024-08-16 23:14:59 UTC  
> Updated at: 2024-08-16 23:16:32 UTC  
> Url: https://github.com/boostorg/random/pull/111  

# Fixed Deprecated Copy Constructor Warning in `polynomial.hpp`  
  
## Issue Description  
  
The `boost/random/detail/polynomial.hpp` triggers compiler warnings related to a deprecated implicit copy constructor:  
  
```  
warning: definition of implicit copy constructor for 'reference' is deprecated because it has a user-declared copy assignment operator [-Wdeprecated-copy]  
```  
  
This warning occurs in C++11 and later standards when a class has a user-declared copy assignment operator but no user-declared copy constructor.  
  
## Cause  
  
The `reference` class has a user-declared copy assignment operator:  
  
```cpp  
reference &operator=(const reference &other)  
```  
  
However, this reference class lacks an explicitly declared copy constructor.  
  
## Backwards-Compatible Fix  
  
To ensure maximum compatibility with various compiler versions, including older ones, we've implemented the following changes:  
  
1. Explicitly defined a copy constructor that performs a member-wise copy. This silences the deprecation warning while maintaining the original behavior:  
  
   ```cpp reference(const reference& other) : _value(other._value), _idx(other._idx) {} ```  
  
2. Omitted the move constructor, ensuring compatibility with pre-C++11 compilers while not affecting functionality. In C++11 and later, moves will be performed as copies, maintaining original behavior.  
  
## Impact of the Fix  
  
This fix resolves the compiler warnings without changing the functionality of the `reference` class.  
  
The fix is backwards-compatible and should not introduce any behavioral changes in existing code.  
  
## Conclusion  
  
This update brings the `polynomial.hpp` file in line with current C++ standards and best practices. It eliminates deprecation warnings while maintaining existing functionality.

---

## Comment 1

> Username: jcstroud  
> Created_at: 2024-08-16 23:16:31 UTC  
> Url: https://github.com/boostorg/random/pull/111#issuecomment-2294434868  

For future reference, a C++11 version of this request can be found here: https://github.com/boostorg/random/pull/110

---
