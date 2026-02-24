# #682 - "Definition is marked dllimport" on mingw [Closed]

> Username: grisumbras  
> Created at: 2022-02-21 08:10:14 UTC  
> Updated at: 2022-02-24 13:25:51 UTC  
> Closed at: 2022-02-24 13:25:51 UTC  
> Url: https://github.com/boostorg/json/issues/682  

```  
./boost/json/parser.hpp: At global scope:  
./boost/json/parser.hpp:469:5: error: function 'std::size_t boost::json::parser::write_some(const char*, std::size_t, std::error_code&)' definition is marked dllimport  
     write_some(  
     ^~~~~~~~~~  
```  
On mingw
