# #16 - Boost testcase fails with llvm and gcc [Closed]

> Username: kamleshbhalui  
> Created at: 2019-07-19 11:28:53 UTC  
> Updated at: 2021-05-18 03:23:22 UTC  
> Closed at: 2021-05-18 03:23:22 UTC  
> Url: https://github.com/boostorg/lambda/issues/16  

One test in  lambda test case [result_of_tests.cpp] is failing  in llvm and gcc compiler.  
  
[https://github.com/boostorg/lambda/blob/develop/test/result_of_tests.cpp#L303]  
  
when compiled with O2 optimization.  
Testcase is trying to return an stack allocated memory reference to caller. Which is actually an undefine behavior.  
  
So when we do store to that stack allocated memory before returning from function,  
 one optimization called Dead Store Elimination pass deletes the store to stack allocated memory in exit block ,Thinking that all these store is dead when we go out of this function scope.  
And It results in above testcase failure.  
Do we need to disable this test or their any other way around?

---

## Comment 1

> Username: pdimov  
> Created at: 2021-05-18 03:23:22 UTC  
> Url: https://github.com/boostorg/lambda/issues/16#issuecomment-842796564  

Fixed by #17.
