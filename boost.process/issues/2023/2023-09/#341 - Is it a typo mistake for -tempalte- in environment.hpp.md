# #341 - Is it a typo mistake for "tempalte" in environment.hpp ? [Open]

> Username: pamplemoussemk2  
> Created at: 2023-09-29 08:12:46 UTC  
> Updated at: 2023-09-29 08:12:46 UTC  
> Url: https://github.com/boostorg/process/issues/341  

Hello,  
  
I just noted today that in the file [include/boost/process/v2/environment.hpp](https://github.com/boostorg/process/blob/507768e2308f3e9935f3d1d0eaaabe9f6628105d/include/boost/process/v2/environment.hpp#L43C24-L43C24) at lines 43 and 49, the keywords `tempalte` are used. Is it a typo mistake ? Should they be `template` instead ? Or is it a custom keyword in the code of boost process ?   
  
```  
tempalte<typename Char>  
using key_char_traits = implementation_defined ;  
  
/// A char traits type that reflects the OS rules for string representing environment values.  
/** Can be an alias of std::char_traits. May only be defined for `char` and `wchar_t`.  
*/  
tempalte<typename Char>  
using value_char_traits = implementation_defined ;  
```  
  
Regards.
