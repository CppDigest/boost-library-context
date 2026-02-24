# #87 Introduce variable_value::as_optional<>() [Open]

> Username: matwey  
> Created at: 2019-11-22 06:59:54 UTC  
> Updated at: 2019-11-22 06:59:54 UTC  
> Url: https://github.com/boostorg/program_options/pull/87  

In some cases, optional<> types are used to specify missed input parameters in  
functions. It may be the case when missed parameter has meaning 'unknown'  
rather than 'default' and requires different behaviour.  
  
Introduce convenient interface for program_options to write code as the  
following:  
  
    const auto opt = va['opt'].as_optional<T>();  
  
as_optional<T>() returns the value when stored type is T,  
empty optional<T> when variable_value is empty,  
or raise bad_cast exception when stored type is other than T (as in case of  
as<T>()).  
  
The latter case is considered as a kind of logic/runtime error since the user  
is supposed to know which type he expects that the variable has.

---
