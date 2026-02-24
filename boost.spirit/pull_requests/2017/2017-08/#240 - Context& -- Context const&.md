# #240 Context& -> Context const& [Merged]

> Username: wanghan02  
> Created at: 2017-08-23 09:47:13 UTC  
> Updated at: 2018-02-19 22:30:23 UTC  
> Merged at: 2017-08-23 17:28:37 UTC  
> Closed at: 2017-08-23 17:28:37 UTC  
> Url: https://github.com/boostorg/spirit/pull/240  

fix parsers like x3::skip(x3::space)[x3::double_] compile problem  
  
x3 directives like skip, with and no_case create an rvalue context therefore cannot bind with Context&. Besides, we are not really changing the context in these places (all the other places use Context const& already). I think using const& is fine here.   
  
The example seems rare, but it could be a blocking issue if we want to use these directives on a generic parser.

---
