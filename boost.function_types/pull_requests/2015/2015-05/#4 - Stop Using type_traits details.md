# #4 Stop Using type_traits details. [Merged]

> Username: jzmaddock  
> Created at: 2015-05-21 18:08:51 UTC  
> Updated at: 2015-05-21 19:16:28 UTC  
> Merged at: 2015-05-21 19:16:28 UTC  
> Closed at: 2015-05-21 19:16:28 UTC  
> Url: https://github.com/boostorg/function_types/pull/4  

Some of the type_traits internal details this header depends on will either go away or be moved.  Either way, best not to rely on another libraries internal details.  In the short term the old code will work but will generate a ton of warnings about using a deprecated header.

---
