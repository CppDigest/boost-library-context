# #829 - revert cc019f75 [Open]

> Username: vinniefalco  
> Created at: 2022-12-28 15:48:49 UTC  
> Updated at: 2022-12-28 15:48:50 UTC  
> Url: https://github.com/boostorg/json/issues/829  

The commit https://github.com/boostorg/json/commit/cc019f753bacfbdd449c72176d435b7c81e28c15 changes a bunch of code into enormous macros, presumably so that file and line number information is more correcterer. While this might have been useful in that moment, we now have the problem that the debugger is not capable of getting a breakpoint at code inside the macro and stepping through it. As I have a failure occurring at code inside the macro, I cannot debug it effectively.  
  
This change needs to be reverted. There is an alternate solution for getting better diagnostics from failing tests, which I will open as another issue.
