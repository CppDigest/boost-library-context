# #380 - Feature request: Integration with clang's __attribute__((analyzer_noreturn)) [Open]

> Username: jonesmz  
> Created at: 2023-03-02 21:02:18 UTC  
> Updated at: 2023-03-02 21:02:18 UTC  
> Url: https://github.com/boostorg/test/issues/380  

When running clang-tidy, the analyzer is able to provide much more succinct problem reports when developers can inform the analyzer "if this function is called, stop, that's the bug".  
  
Frequently, boost test based unit tests can give the analyzer fits where it dumps out hundreds or thousands of lines of additional analysis pertaining to the boost test framework, when it should really have stopped at the assertion failing instead of continuing to digest all of the inner machinery from the framework.  
  
To enable this enhanced analysis, it's sufficient to check in the preprocessor  
```  
__has_feature(attribute_analyzer_noreturn)  
```  
and if that's true, then add  
```  
__attribute__((analyzer_noreturn))  
```  
to some appropriate function that is only called when a test case fails.
