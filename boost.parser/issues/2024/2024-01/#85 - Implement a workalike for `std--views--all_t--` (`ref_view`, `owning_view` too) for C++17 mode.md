# #85 - Implement a workalike for `std::views::all_t<>` (`ref_view`, `owning_view` too) for C++17 mode [Closed]

> Username: tzlaine  
> Created at: 2024-01-27 22:15:20 UTC  
> Updated at: 2024-01-28 22:45:41 UTC  
> Closed at: 2024-01-28 22:45:41 UTC  
> Url: https://github.com/boostorg/parser/issues/85  

Right now, if you do this:  
  
```c++  
std::string s;  
s | boost::parser::as_utf8;  
```  
  
You get a `utf8_view<std::string>`.  That is, the `std::string` gets copied into the `utf8_view`; not what we want!  
  
This all works fine for C++20 of course.
