# #23 - move_only_function does not carry mutable lambda state over multiple calls [Closed]

> Username: ccaughie  
> Created at: 2026-01-15 04:48:47 UTC  
> Updated at: 2026-01-22 07:49:56 UTC  
> Closed at: 2026-01-22 07:49:56 UTC  
> Url: https://github.com/boostorg/compat/issues/23  

boost::compat::move_only_function appears to differ from std::move_only_function when used with a mutable lamba that modifies captured variables. Specifically, the captured value appears to revert to its initial state each time the wrapped function is called, instead of preserving the new state as one would expect.  
  
This can be demonstrated with the following program:  
  
```  
#include <boost/compat/move_only_function.hpp>  
#include <iostream>  
  
template<typename Sig>  
using move_only_function = boost::compat::move_only_function<Sig>;  
  
// For comparison:  
// using move_only_function = std::move_only_function<Sig>;  
  
int main()  
{  
    int captured = 0;  
    move_only_function<int()> func = [captured]() mutable { return ++captured; };  
  
    int result = func();  
    std::cout << "Result 1: " << result << "\n";  
  
    result = func();  
    std::cout << "Result 2: " << result << "\n";  
}  
```  
  
Output with the Boost version:  
```  
Result 1: 1  
Result 2: 1  
```  
  
Output with the std version:  
```  
Result 1: 1  
Result 2: 2  
```  
  
Tested with Boost 1.90.0 using gcc 13.3.0 on Ubuntu 24.04.

---

## Comment 1

> Username: cmazakas  
> Created at: 2026-01-15 22:18:13 UTC  
> Url: https://github.com/boostorg/compat/issues/23#issuecomment-3757152515  

@ccaughie I have a PR open here https://github.com/boostorg/compat/pull/24 that should address this issue  
  
I'm not stoked on the `const_cast` I had to use so hopefully this break variance and we still wind up invoking correctly, ha ha. But tests seem to pass.  
  
I'll probably wind up taking a closer look at this a bit later.
