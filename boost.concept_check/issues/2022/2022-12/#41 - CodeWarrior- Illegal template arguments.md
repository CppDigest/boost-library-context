# #41 - CodeWarrior: Illegal template arguments [Open]

> Username: jwillikers  
> Created at: 2022-12-05 14:37:46 UTC  
> Updated at: 2022-12-05 14:39:24 UTC  
> Url: https://github.com/boostorg/concept_check/issues/41  

Boost Version: 1.79.0  
Platform: Baremetal PowerPC  
Compiler: CodeWarrior  
Using C++03.  
  
The failed template thingy is causing compilation to fail with CodeWarrior with the error `illegal template arguments` on [this line](https://github.com/boostorg/concept_check/blob/develop/include/boost/concept/detail/general.hpp#L44) and [this line](https://github.com/boostorg/concept_check/blob/develop/include/boost/concept/detail/general.hpp#L76). This occurs when trying to use Boost circular_buffer. I'm guessing it's due to the asterisks?
