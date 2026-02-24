# #59 - import_mangled fails with clang and std::string [Open]

> Username: ivan-mci  
> Created at: 2023-02-05 23:44:24 UTC  
> Updated at: 2025-01-28 10:03:57 UTC  
> Url: https://github.com/boostorg/dll/issues/59  

I have a plugin.so file with the following function:  
```  
namespace plugin  
{  
  void InitPlugin(const std::string &path)  
  {  
  }  
}  
  
```  
  
I am trying to load the function using import_mangled. I am aware that it is experimental.  
```  
boost::dll::experimental::smart_library lib("./plugin.so");  
typedef void (InitPlugin_t)(const std::string&);  
auto initPlugin = boost::dll::experimental::import_mangled<InitPlugin_t>(lib, "plugin::InitPlugin");  
  
```  
  
This works fine with g++, but fails with clang. Both under Ubuntu 22.04.   
plugin.so exports a function named:  
**plugin::InitPlugin(std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > const&)**  
  
However it looks like import_mangled under clang is looking for a function named:  
**plugin::InitPlugin(std::basic_string<char, std::char_traits<char>, std::allocator<char> > const&)**  
Notice that the namespace __cxx11 is missing.  
  
It seems to come from a difference between how `__PRETTY_FUNCTION__` is implemented in g++ and clang.
