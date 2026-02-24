# #127 - w32_regex_traits.hpp unconditionally defines WIN32_LEAN_AND_MEAN [Closed]

> Username: ngladitz  
> Created at: 2021-04-19 13:34:30 UTC  
> Updated at: 2021-06-06 08:07:46 UTC  
> Closed at: 2021-06-06 08:07:46 UTC  
> Url: https://github.com/boostorg/regex/issues/127  

While trying to update to Boost 1.76.0 I started getting warnings in MSVC windows builds:  
```  
boost/regex/v5/w32_regex_traits.hpp(33): warning C4005: 'WIN32_LEAN_AND_MEAN': macro redefinition  
```  
  
I don't directly use boost/regex; I think ASIO is pulling it in.  
  
The project globally defines `WIN32_LEAN_AND_MEAN`.   
`w32_regex_traits.hpp` also (unconditionally) defines the macro leading to the above redefinition warning.  
  
Presumably the header should check for `WIN32_LEAN_AND_MEAN` before defining it.  
Same for `VC_EXTRALEAN`.  
  
Perhaps also make the definition opt-out in case someone needs the non-lean-and-mean version(?).  
ASIO seems to use `BOOST_ASIO_NO_WIN32_LEAN_AND_MEAN` for this.

---

## Comment 1

> Username: jzmaddock  
> Created at: 2021-06-06 08:03:56 UTC  
> Url: https://github.com/boostorg/regex/issues/127#issuecomment-855357404  

Confirmed.
