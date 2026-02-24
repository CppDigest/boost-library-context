# #12 - How to capture by reference? [Open]

> Username: denzor200  
> Created at: 2025-03-15 22:26:27 UTC  
> Updated at: 2025-03-18 12:11:37 UTC  
> Url: https://github.com/boostorg/lambda2/issues/12  

Is it possible with Boost Lambda 2? Is that truth that this library forces the users to capture by value?

---

## Comment 1

> Username: pdimov  
> Created at: 2025-03-16 00:23:42 UTC  
> Url: https://github.com/boostorg/lambda2/issues/12#issuecomment-2727081912  

`std::ref` should work. Is there a specific example where there's a problem?

---

## Comment 2

> Username: denzor200  
> Created at: 2025-03-18 12:11:36 UTC  
> Url: https://github.com/boostorg/lambda2/issues/12#issuecomment-2732997006  

> Is there a specific example where there's a problem?  
  
Nope, not a specific use case at all, moreover - I'm not going to capture by reference via this library in a real code :)  
  
The situation is that I am currently collecting ideas for improving the Clang Tidy static analysis tool.  
Recently I've submitted nice Feature Request(https://github.com/llvm/llvm-project/issues/120863) to seek and hint UB that's created due to incorrect C++11 lambda usage.  
  
Then all I want to know is whether it is possible to reproduce the same UB by incorrectly using Boost Lambda 2 instead of C++11 lambda.  
  
> std::ref should work.  
  
Hmmm, sounds interesting. I will try `std::ref` to reproduce that UB
