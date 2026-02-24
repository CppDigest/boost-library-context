# #126 - Rework error_code internal data members [Open]

> Username: pdimov  
> Created at: 2024-09-14 16:02:53 UTC  
> Updated at: 2024-09-14 16:02:53 UTC  
> Url: https://github.com/boostorg/system/issues/126  

Instead of  
```  
    struct data  
    {  
        int val_;  
        const error_category * cat_;  
    };  
  
    union  
    {  
        data d1_;  
        unsigned char d2_[ sizeof(std::error_code) ];  
    };  
```  
it might be better to have  
```  
int val_;  
void const* cat_; // error_category or std::error_category  
```  
which could both simplify the code (esp. when we transition the `std::error_code` case to (value, unknown_category)) and help with GCC false -Wmaybe-uninitialized positives.
