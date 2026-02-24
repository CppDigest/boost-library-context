# #176 Add `index` variable template to get index of member pointer [Open]

> Username: uyha  
> Created at: 2024-08-12 13:52:57 UTC  
> Updated at: 2024-09-19 07:03:06 UTC  
> Url: https://github.com/boostorg/pfr/pull/176  

closes #60

---

## Comment 1

> Username: uyha  
> Created_at: 2024-08-12 13:55:59 UTC  
> Url: https://github.com/boostorg/pfr/pull/176#issuecomment-2284061009  

@apolukhin I could not find the document on how to contribute, could you direct me on the following matters?  
- How to run the tests locally? How do I add test?  
- I use `requires` in the implementation, should I drop to SFINAE to support C++17?  
- There is a case that the index cannot be found, what should I do there?

---

## Comment 2

> Username: apolukhin  
> Created_at: 2024-09-19 07:03:06 UTC  
> Url: https://github.com/boostorg/pfr/pull/176#issuecomment-2360154672  

> How to run the tests locally? How do I add test?  
```  
cd boost/libs/pfr/tests  
../../../b2 cxxstd=17  
```  
  
> I use requires in the implementation, should I drop to SFINAE to support C++17?  
  
Yes, please  
  
> There is a case that the index cannot be found, what should I do there?  
  
How about returning std::optional ? Or you could throw an exception. Or return -1. Write some samples and experiment to get the idea of which error reporting mechanic is better for that function.

---
