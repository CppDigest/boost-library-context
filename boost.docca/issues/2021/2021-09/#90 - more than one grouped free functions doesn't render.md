# #90 - more than one grouped free functions doesn't render [Open]

> Username: vinniefalco  
> Created at: 2021-09-14 22:46:27 UTC  
> Updated at: 2021-09-14 22:46:27 UTC  
> Url: https://github.com/boostorg/docca/issues/90  

If you group 2 friends, it works. But if you try to group another 2 friends, it stops working.  
```  
struct T {  
  friend void f1a();  
  friend void f1b();  
  friend void f2a();  
  friend void f2b();  
  friend void f3a();  
  friend void f3b();  
  };  
  
///@{  
/** First group  
*/  
void f1a();  
  
void f1b();  
///@}  
  
///@{  
/** Second group  
*/  
void f2a();  
  
void f2b();  
///@}  
  
/** Third single function  
*/  
void f3a();  
  
/** Third single function  
*/  
void f3b();  
```  
  
Result: The 2nd element of each group doesn't have a javadoc. If you remove the 2nd group markings, then the 2nd element of the first group renders. If you can't repro this I can show you in the actual library where it happens, in case it is contextual.
