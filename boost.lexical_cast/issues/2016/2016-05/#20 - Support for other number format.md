# #20 - Support for other number format [Closed]

> Username: teeks99  
> Created at: 2016-05-31 15:46:21 UTC  
> Updated at: 2016-08-31 18:35:45 UTC  
> Closed at: 2016-08-31 18:35:44 UTC  
> Url: https://github.com/boostorg/lexical_cast/issues/20  

C++ supports a few [other number formats (for integers) as literals](http://en.cppreference.com/w/cpp/language/integer_literal)  
- Hex - 0x1a2b034f, 0X1A2B034F  
- Octal - 012456780  
- Binary (C++ 14) - 0b10010  
  
It would be ideal if lexical_cast matches these.

---

## Comment 1

> Username: apolukhin  
> Created at: 2016-08-31 18:35:44 UTC  
> Url: https://github.com/boostorg/lexical_cast/issues/20#issuecomment-243858739  

This will not be implemented because it's against the basic design of a lexical_cast and it changes the behavior of existing programs.  
  
For more involved conversions, such as where precision or formatting need tighter control than is offered by the default behavior of lexical_cast, the conventional std::stringstream approach is recommended.
