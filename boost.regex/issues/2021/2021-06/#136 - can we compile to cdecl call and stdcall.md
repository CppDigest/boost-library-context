# #136 - can we compile to cdecl call and stdcall? [Closed]

> Username: loquat  
> Created at: 2021-06-07 05:04:35 UTC  
> Updated at: 2021-06-11 15:56:49 UTC  
> Closed at: 2021-06-11 15:56:48 UTC  
> Url: https://github.com/boostorg/regex/issues/136  

we need vb6 to use this library.

---

## Comment 1

> Username: jzmaddock  
> Created at: 2021-06-11 15:56:48 UTC  
> Url: https://github.com/boostorg/regex/issues/136#issuecomment-859678618  

Only by changing the default calling convention when building.  But if you're calling from VB6 you would probably want to define you're own API and have that call into the library.
