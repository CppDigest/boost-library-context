# #93 regex_traits_defaults: Do not export arrays only used in same compila… [Merged]

> Username: rdoeffinger  
> Created at: 2019-11-08 10:15:01 UTC  
> Updated at: 2020-01-07 18:56:06 UTC  
> Merged at: 2020-01-07 18:56:06 UTC  
> Closed at: 2020-01-07 18:56:06 UTC  
> Url: https://github.com/boostorg/regex/pull/93  

…tion unit.  
  
Exporting variables is unreliable on some platforms including Windows,  
and it seems can cause compilation errors in some cases.  
Also symbols should not be exported unnecessarily purely on principle.  
It would even be possible to move the arrays into the function, but  
as they are a bit large that seems to be worse for readability.

---
