# #122 - conversion warning on MSVC (VS 2022 17.9.1, toolset v143, std=C++latest) [Closed]

> Username: tobias-loew  
> Created at: 2024-02-27 13:41:35 UTC  
> Updated at: 2024-03-15 21:29:32 UTC  
> Closed at: 2024-03-15 21:29:32 UTC  
> Url: https://github.com/boostorg/parser/issues/122  

file include\boost\parser\detail\printing_impl.hpp  
```  
                detail::print_parser(context, parser, os, components);  
                ++components;  
                ++i;  
                prev_group = group;  
                          ^^^  
```
