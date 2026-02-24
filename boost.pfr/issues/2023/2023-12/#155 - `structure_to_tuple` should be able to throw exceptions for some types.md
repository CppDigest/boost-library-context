# #155 - `structure_to_tuple` should be able to throw exceptions for some types [Closed]

> Username: denzor200  
> Created at: 2023-12-26 21:04:59 UTC  
> Updated at: 2024-02-15 07:10:48 UTC  
> Closed at: 2024-02-15 07:10:48 UTC  
> Url: https://github.com/boostorg/pfr/issues/155  

**Snipped:**  
https://godbolt.org/z/5soefb4Ee  
  
**Expected behaviour:**  
to print `CAUGHT!`  
  
**Observed behaviour:**  
```  
terminate called after throwing an instance of 'std::runtime_error'  
  what():  just exception  
Program terminated with signal: SIGSEGV  
```
