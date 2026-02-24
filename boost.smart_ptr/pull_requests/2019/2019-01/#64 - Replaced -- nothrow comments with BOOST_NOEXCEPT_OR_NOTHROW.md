# #64 Replaced // nothrow comments with BOOST_NOEXCEPT_OR_NOTHROW [Closed]

> Username: jlepola  
> Created at: 2019-01-28 14:38:37 UTC  
> Updated at: 2019-01-29 07:57:16 UTC  
> Closed at: 2019-01-29 07:57:15 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/64  

rvalue-constructors triggered a warning "C26439 SPECIAL_NOEXCEPT" if code analysis was set to level "Microsoft Native Recommended Rules" when compiling with Visual Studio 2017. See https://docs.microsoft.com/en-us/visualstudio/code-quality/c26439?view=vs-2017  
  
Also, it is better to use language constructs to represent semantics when possible. E.g. it is now possible to store objects of the affected classes in a vector and the vector no longer has to call the copy constructors when resizing its internal buffer. Not sure how relevant it is for these particular objects though.  
  
This resolves issue #63

---

## Comment 1

> Username: jlepola  
> Created_at: 2019-01-29 07:57:15 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/64#issuecomment-458442052  

The issue was resolved in the commit 51d8167fbf0f9c8cb28fd50080bac38366cebbd5 with a macro that is more appropriate for the smart_ptr submodule. I'll close this merge request.

---
