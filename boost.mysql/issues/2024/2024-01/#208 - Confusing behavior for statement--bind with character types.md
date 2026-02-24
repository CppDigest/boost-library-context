# #208 - Confusing behavior for statement::bind with character types [Closed]

> Username: anarthal  
> Created at: 2024-01-12 10:08:17 UTC  
> Updated at: 2024-02-18 11:34:02 UTC  
> Closed at: 2024-02-18 11:34:02 UTC  
> Url: https://github.com/boostorg/mysql/issues/208  

This is correct:  
  
```  
stmt.bind(uint8_t(42)); // understood as an integer  
```  
  
But this is confusing and should probably not be allowed:  
  
```  
stmt.bind('a'); // understood as an integer too, 97  
```  
  
Applies to `char`, `wchar_t`, and `charN_t`
