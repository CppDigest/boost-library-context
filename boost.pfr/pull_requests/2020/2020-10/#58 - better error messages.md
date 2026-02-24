# #58 better error messages [Merged]

> Username: akrzemi1  
> Created at: 2020-10-19 20:15:15 UTC  
> Updated at: 2020-10-20 18:27:15 UTC  
> Merged at: 2020-10-20 18:26:56 UTC  
> Closed at: 2020-10-20 18:26:56 UTC  
> Url: https://github.com/boostorg/pfr/pull/58  

Before this change, when I compile a program that uses an aggregate with a base class, I get the following message in clang and GCC with C++17:  
  
```  
error: type 'some_person' decomposes into 2 elements, but 3 names were provided  
  auto& [a,b,c] = val;  
```  
  
After this change, I get error message:  
  
```  
error: type 'some_person' decomposes into 2 elements, but 3 names were provided  
  auto& [a,b,c] = val;  // ====================> Boost.PFR: User-provided type is not a SimpleAggregate.  
```  
  
Of course, this change makes the programmer's life easier only when name `SimpleAggregate` is defined and referenced in the docs. Hence the changes in docs also.

---

## Comment 1

> Username: apolukhin  
> Created_at: 2020-10-20 18:27:15 UTC  
> Url: https://github.com/boostorg/pfr/pull/58#issuecomment-713052569  

Many thanks! Great idea

---
