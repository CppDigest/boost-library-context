# #980 - Compilation Issue Due to Method Definitions in Both '.cpp' and '.hpp' Files in 'greg_weekday' Class [Open]

> Username: Vignesh-Murugappan  
> Created at: 2024-11-13 14:58:32 UTC  
> Updated at: 2024-11-13 14:58:32 UTC  
> Url: https://github.com/boostorg/boost/issues/980  

The methods in the **"greg_weekday"** class are defined in both the **.cpp and .hpp** files, which causes a compilation issue.  
  
For example, the **as_short_string** method is defined in **greg_weekday.cpp** as follows:  
  
```cpp  
//! Return a 3-digit English string of the day of the week (e.g., Sun)  
const char* greg_weekday::as_short_string() const   
{  
    return short_weekday_names[value_];  
}  
```  
  
However, the same method is also defined in the **.hpp** file as:  
  
```cpp  
//! Return a 3-digit English string of the day of the week (e.g., Sun)  
const char* as_short_string() const  
{  
    static const char* const short_weekday_names[]   
        = {"Sun", "Mon", "Tue", "Wed", "Thu", "Fri", "Sat"};  
  
    return short_weekday_names[value_];  
}  
```
