# #105 - Constrain `parse*()` to require that `Attr` is not a parser [Closed]

> Username: tzlaine  
> Created at: 2024-02-22 07:59:28 UTC  
> Updated at: 2024-03-15 21:29:27 UTC  
> Closed at: 2024-03-15 21:29:27 UTC  
> Url: https://github.com/boostorg/parser/issues/105  

This works:  
  
```  
return parse(s, input, bp::ws|bp::lit(','));  
```  
  
This blows up:  
  
```c++  
  auto skipper = bp::ws | bp::lit(',');  
  return parse(s, input, skipper);  
```  
  
This is because the second case thinks that `skipper` is an out-param.
