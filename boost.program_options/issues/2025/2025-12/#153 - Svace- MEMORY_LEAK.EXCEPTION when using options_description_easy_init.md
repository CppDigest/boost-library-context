# #153 - Svace: MEMORY_LEAK.EXCEPTION when using options_description_easy_init [Closed]

> Username: vudeam  
> Created at: 2025-12-29 08:28:20 UTC  
> Updated at: 2026-01-22 11:38:22 UTC  
> Closed at: 2026-01-22 11:38:22 UTC  
> Url: https://github.com/boostorg/program_options/issues/153  

I've stumbled upon this issue when the project using Boost.ProgramOptions was tested by [Svace static analyzer](https://www.ispras.ru/en/technologies/svace/).  
  
Here is the minimal reproducible example:  
  
```cpp  
#include <boost/make_shared.hpp>  
#include <boost/program_options.hpp>  
  
#include <iostream>  
#include <string>  
#include <vector>  
  
namespace {  
void f(const std::vector<std::string>& args)  
{  
    using namespace boost::program_options;  
  
    variables_map vm{};  
  
    try  
    {  
        options_description desc{};  
  
        desc.add_options()  
            ("foo", value<std::string>())  
        ;  
  
        // desc.add(boost::make_shared<option_description>("foo", value<std::string>()));  
  
        store(command_line_parser(args).style(command_line_style::default_style).options(desc).run(), vm);  
        notify(vm);  
    }  
    catch (const std::exception& ex)  
    {  
        std::cerr << "Error: " << ex.what() << "\n";  
    }  
  
    if (vm.contains("foo"))  
    {  
        std::cout << "foo=" << vm["foo"].as<std::string>() << "\n";  
    }  
}  
}  
  
int main(int argc, char* argv[])  
{  
    std::vector<std::string> args{argv, argv + argc};  
    f(args);  
  
    return 0;  
}  
```  
  
Svace gives the following diagnostics for the program provided:  
  
```  
Function: _ZN12_GLOBAL__N_11fERKSt6vectorINSt7__cxx1112basic_stringIcSt11char_traitsIcESaIcEEESaIS6_EE  
Warning message: Dynamic memory, referenced by '((struct value_semantic*)return value of boost::program_options::value<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >...', is allocated at main.cpp:20 by calling function 'boost::program_options::value<std::__cxx11::basic_string<..> >' and could be lost if an exception is thrown at main.cpp:37. (CWE401, CWE775)  
```  
  
However, if the option is added using `.add` instead of `.add_options` (as in commented out line 23 before the `.store` call) the message about memory leak is not generated.  
  
Boost libraries are version 1.83.0

---

## Comment 1

> Username: vprus  
> Created at: 2025-12-29 08:32:31 UTC  
> Url: https://github.com/boostorg/program_options/issues/153#issuecomment-3695845043  

Thanks for the report!  
  
Unfortunately, there is a million of various static analyzers out there. I would be able to look at an issue only if you analyze the finding, confirm they are true, and formulate the problem directly without reference to any tool.  
  
Could you do that?

---

## Comment 2

> Username: vprus  
> Created at: 2026-01-22 11:38:22 UTC  
> Url: https://github.com/boostorg/program_options/issues/153#issuecomment-3783937422  

I am afraid this issue is not actionable as it is, closing.
