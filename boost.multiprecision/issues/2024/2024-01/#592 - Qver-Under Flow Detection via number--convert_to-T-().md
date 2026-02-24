# #592 - Qver/Under Flow Detection via number::convert_to<T>() [Open]

> Username: PaltryProgrammer  
> Created at: 2024-01-28 18:34:21 UTC  
> Updated at: 2024-01-28 19:19:52 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/592  

greetings & kind regards  
`number::convert_to<T>()` as near as i can discern does not report under/overflow . is there a means to detect same? other than exempli gratia :  
  
```  
cpp_int _cpp_int = 0x159357;  
auto _int =_cpp_int.convert_to<int>();   
auto _overflow = _int != _cpp_int;  
```  
thank you kindly
