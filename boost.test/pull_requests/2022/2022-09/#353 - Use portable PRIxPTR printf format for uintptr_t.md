# #353 Use portable PRIxPTR printf format for uintptr_t [Merged]

> Username: apolukhin  
> Created at: 2022-09-08 10:46:13 UTC  
> Updated at: 2023-06-08 07:14:02 UTC  
> Merged at: 2023-06-08 07:14:02 UTC  
> Closed at: 2023-06-08 07:14:02 UTC  
> Url: https://github.com/boostorg/test/pull/353  

On some platforms uintptr_t is not the 'unsigned long' type. To avoid warnings the PRIxPTR format specifier is used

---
