# #149 - `to_chars_hex` for __float128 incorrect exp [Closed]

> Username: mborland  
> Created at: 2024-02-01 08:19:27 UTC  
> Updated at: 2024-02-02 10:44:31 UTC  
> Closed at: 2024-02-02 10:44:31 UTC  
> Url: https://github.com/boostorg/charconv/issues/149  

Test value: -3.589653987658756543653653365436e+04  
  
```  
(std  , hex       )	-1.1871146ab43ef0735e163704ac67p+15	(35, Success)  
(boost, hex       )	-1.1871146ab43ef0735e163704ac67p+32783	(38, Success)  
```
