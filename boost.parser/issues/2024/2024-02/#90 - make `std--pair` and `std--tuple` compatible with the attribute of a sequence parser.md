# #90 - make `std::pair` and `std::tuple` compatible with the attribute of a sequence parser [Closed]

> Username: akrzemi1  
> Created at: 2024-02-03 13:30:07 UTC  
> Updated at: 2024-02-04 23:08:27 UTC  
> Closed at: 2024-02-04 23:08:27 UTC  
> Url: https://github.com/boostorg/parser/issues/90  

Is it possible to make `std::pair` and `std::tuple` compatible with the attribute of a sequence parser:  
  
```c++  
namespace parser  
{  
    const auto string = '"' >> bp::lexeme[*(bp::char_ - '"')] > '"';  
    const auto specifier = string > ':' > string;  
}  
  
int main()  
{  
    std::string input = R"( "dd" : "2" )";  
    std::pair<std::string, std::string> result;  
    
    auto b = bp::parse(input, parser::specifier, bp::ws, result);  
}  
```  
  
It intuitively expected that I would represent a config file as a vector of pairs and it would Just Work®.  
  
A ful example that I would like to work:  
https://godbolt.org/z/jKxv4a1hY

---

## Comment 1

> Username: tzlaine  
> Created at: 2024-02-04 22:55:01 UTC  
> Updated at: 2024-02-04 22:55:39 UTC  
> Url: https://github.com/boostorg/parser/issues/90#issuecomment-1925955343  

This also appears to result from a stale unified header build, because that code works fine for me.  That build is not longer working, and I have no idea why.  I've removed reference to it from the landing page until I can figure it out.  It's a pretty low priority, though.
