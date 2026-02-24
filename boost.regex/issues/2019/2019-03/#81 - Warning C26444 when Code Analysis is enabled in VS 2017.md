# #81 - Warning C26444 when Code Analysis is enabled in VS 2017 [Closed]

> Username: jlepola  
> Created at: 2019-03-07 15:07:31 UTC  
> Updated at: 2020-01-22 18:28:12 UTC  
> Closed at: 2020-01-22 18:28:12 UTC  
> Url: https://github.com/boostorg/regex/issues/81  

The warning [C26444](https://docs.microsoft.com/en-us/visualstudio/code-quality/c26444?view=vs-2017) is caused by the fact that the constructor ignores the return value of the imbue method. The warning goes away if the return value is assigned to a temporary variable.  
  
This could be fixed with any one of the following methods:  
* Ignore the warning with #pragma  
* Initialize the relevant members directly without invoking the "imbue" method. This duplicates logic.  
* Store the return value to a named variable. Does this affect compiler optimizations?  
  
Which one would be the most appropriate for this project?  
  
The example project in #80 can be used to reproduce this issue as well.  
  
Warning: `c:\git\boost\boost\regex\v4\cpp_regex_traits.hpp(177): warning C26444: Avoid unnamed objects with custom construction and destruction (es.84)`

---

## Comment 1

> Username: jzmaddock  
> Created at: 2020-01-22 18:05:29 UTC  
> Url: https://github.com/boostorg/regex/issues/81#issuecomment-577312216  

This was fixed as part of https://github.com/boostorg/regex/commit/2cd947f7c41174b1d9f8587801b6e997ad2a9a29.  
  
Thanks for the report!
