# #63 - rvalue-constructors produce warning C26439 when code analysis is enabled in VS 2017 [Closed]

> Username: jlepola  
> Created at: 2019-01-28 08:53:44 UTC  
> Updated at: 2019-04-20 16:11:21 UTC  
> Closed at: 2019-04-20 16:11:21 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/63  

When code analysis is set to level "Microsoft Native Recommended Rules", Visual Studio 2017 expects that all rvalue-constructors have the noexcept specifier. This causes a warning: "[warning C26439: This kind of function may not throw. Declare it 'noexcept' (f.6).](https://docs.microsoft.com/en-us/visualstudio/code-quality/c26439?view=vs-2017)" to be emitted with boost version 1.69.  
  
Various methods have a comment `// nothrow`, including the rvalue-constructors, but I could not understand why the macro `BOOST_NOEXCEPT_OR_NOTHROW` was not used here instead. It would make sense to use the actual language construct / specifier to declare this fact.  
  
I will create a pull request which replaces all instances of the `// nothrow` comment with the `BOOST_NOEXCEPT_OR_NOTHROW` macro. But in case there is a technical reason why this cannot be done then that discussion can take place in the scope of this issue. If there is a valid technical reason then we can simply suppress those warnings.  
  
#### Exact warnings  
*\boost\smart_ptr\detail\shared_count.hpp(442): warning C26439: This kind of function may not throw. Declare it 'noexcept' (f.6).  
*\boost\smart_ptr\detail\shared_count.hpp(559): warning C26439: This kind of function may not throw. Declare it 'noexcept' (f.6).

---

## Comment 1

> Username: jlepola  
> Created at: 2019-01-28 14:39:35 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/63#issuecomment-458156791  

Associated pull request #64
