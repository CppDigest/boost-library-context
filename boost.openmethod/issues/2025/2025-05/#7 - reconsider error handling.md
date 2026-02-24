# #7 - reconsider error handling [Closed]

> Username: jll63  
> Created at: 2025-05-02 02:04:46 UTC  
> Updated at: 2025-06-19 21:35:31 UTC  
> Closed at: 2025-06-08 21:53:48 UTC  
> Url: https://github.com/boostorg/openmethod/issues/7  

Make throwing exceptions the default?

---

## Comment 1

> Username: jll63  
> Created at: 2025-06-08 21:53:21 UTC  
> Url: https://github.com/boostorg/openmethod/issues/7#issuecomment-2954292944  

I think the current way is the right way. Exceptions should be used to report errors that occur during the execution of a correct program. The errors that the library reports are actually bugs in the program. They are in the same category as inadvertently calling a pure virtual function in a constructor. This typically results in a brief error message, then program termination via `abort`.  Also throwing exceptions (by default) to report errors would not work if exceptions are disabled. Thus the default would have to be different.
