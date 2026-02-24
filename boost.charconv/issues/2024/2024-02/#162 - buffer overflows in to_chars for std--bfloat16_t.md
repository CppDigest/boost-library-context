# #162 - buffer overflows in to_chars for std::bfloat16_t [Closed]

> Username: gpeterhoff  
> Created at: 2024-02-10 07:14:38 UTC  
> Updated at: 2024-02-15 11:06:46 UTC  
> Closed at: 2024-02-15 11:06:46 UTC  
> Url: https://github.com/boostorg/charconv/issues/162  

Hi Matt,  
if the buffer is too small and value = ±inf/±(s)nan there will be buffer overflows again. Here:  
```  
using buffer_type = std::array<char, 2>;  
value = -L::quiet_NaN();  
```  
  
```  
std::bfloat16_t  
(std  , scientific)	{}	(2, Value too large for defined data type)  
(boost, scientific)	BOF!	(9, Success)  
  
(std  , fixed     )	{}	(2, Value too large for defined data type)  
(boost, fixed     )	BOF!	(9, Success)  
  
(std  , hex       )	{}	(2, Value too large for defined data type)  
(boost, hex       )	{}	(2, Numerical result out of range)  
  
(std  , general   )	{}	(2, Value too large for defined data type)  
(boost, general   )	BOF!	(9, Success)  
```  
  
regards  
Gero
