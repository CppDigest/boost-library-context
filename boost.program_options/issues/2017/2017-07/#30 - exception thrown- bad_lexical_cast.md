# #30 - exception thrown: bad_lexical_cast [Closed]

> Username: thomas955  
> Created at: 2017-07-19 09:44:07 UTC  
> Updated at: 2017-07-25 13:37:54 UTC  
> Closed at: 2017-07-24 18:48:36 UTC  
> Url: https://github.com/boostorg/program_options/issues/30  

Hi,  
  
we discovered, that boost crashes with "bad_lexical_cast" if we provide an empty "string" as parameter.  
Tested with boost 1.58.0 and 1.63.0  
  
Cli input to reproduce:    sleep ""  
  
gdb Backtrace (catch throw) with boost_ver: 1.58.0  
  
```  
#0  0x00007ffff2cbbd90 in __cxa_throw () from /usr/lib/x86_64-linux-gnu/libstdc++.so.6  
#1  0x000000000049a12e in boost::throw_exception<boost::bad_lexical_cast> (e=...) at path/gcc48/include/boost-1_58_1/boost/throw_exception.hpp:69  
#2  0x00007ffff739ea91 in boost::conversion::detail::throw_bad_cast<std::string, unsigned int> () at path/gcc48/include/boost-1_58_1/boost/lexical_cast/bad_lexical_cast.hpp:92  
#3  0x00007ffff739e123 in boost::lexical_cast<unsigned int, std::string> (arg="") at path/gcc48/include/boost-1_58_1/boost/lexical_cast.hpp:42  
#4  0x00007ffff739d901 in boost::program_options::validate<unsigned int, char> (v=..., xs=std::vector of length 1, capacity 1 = {...})  
    at path/gcc48/include/boost-1_58_1/boost/program_options/detail/value_semantic.hpp:89  
#5  0x00007ffff739d44c in boost::program_options::typed_value<unsigned int, char>::xparse (this=0x70fff0, value_store=..., new_tokens=std::vector of length 1, capacity 1 = {...})  
    at path/gcc48/include/boost-1_58_1/boost/program_options/detail/value_semantic.hpp:167  
#6  0x00007ffff4c6f0b5 in boost::program_options::value_semantic_codecvt_helper<char>::parse(boost::any&, std::vector<std::string, std::allocator<std::string> > const&, bool) const ()  
   from path/gcc48/lib/x86_64-linux-gnu/libboost_program_options-gcc48-mt-1_58_1.so.1.58.1  
#7  0x00007ffff4c6b1da in boost::program_options::store(boost::program_options::basic_parsed_options<char> const&, boost::program_options::variables_map&, bool) ()  
   from path/gcc48/lib/x86_64-linux-gnu/libboost_program_options-gcc48-mt-1_58_1.so.1.58.1  
#8  0x00007ffff74a08f8 in program_name::cli::namespace_version::bindCommandline (tokens=..., startIdx=0, opts=..., popts=..., vmap=...)  
```  
  
content of 'boost::program_options::basic_parsed_options<char> const&' :  
  
```  
{options = std::vector of length 1, capacity 1 = {{string_key = "time", position_key = 0, value = std::vector of length 1, capacity 1 = {""}, original_tokens = std::vector of length 1, capacity 1 = {""},   
      unregistered = false, case_insensitive = false}}, description = 0x7ffff788b620 <program_name::cli::namespace_version::POConsoleInput::s_sleepOptions>, m_options_prefix = 1}  
```  
  
Thank you in advance

---

## Comment 1

> Username: vprus  
> Created at: 2017-07-19 10:29:22 UTC  
> Url: https://github.com/boostorg/program_options/issues/30#issuecomment-316342933  

Thanks for the report. Do you have a minimal example that reproduces the issue?

---

## Comment 2

> Username: thomas955  
> Created at: 2017-07-19 11:42:50 UTC  
> Url: https://github.com/boostorg/program_options/issues/30#issuecomment-316359348  

```  
#include <cstdint>  
#include <iostream>  
#include <vector>  
  
#include <boost/program_options.hpp>  
  
using namespace std;  
  
using namespace boost::program_options;  
  
options_description opts;  
positional_options_description popts;  
  
int main()  
{  
        try  
        {  
                opts.add_options()("foo", value<uint32_t>()->value_name("<time>")->required());  
                popts.add("foo", 1);   
                vector<const char *> tokens{"bar", ""};  
                auto parsed = command_line_parser(2, &tokens.front())  
                    .style(command_line_style::default_style & ~command_line_style::allow_guessing)  
                    .options(opts)  
                    .positional(popts)  
                    .run();  
                variables_map vmap;  
                store(parsed, vmap);  
        }  
        catch ( const std::exception &ex )  
        {  
                std::cout << "D'oh! " << ex.what() << '\n';  
        }  
        std::cout << "The end.\n";  
}  
```

---

## Comment 3

> Username: vprus  
> Created at: 2017-07-21 15:41:01 UTC  
> Url: https://github.com/boostorg/program_options/issues/30#issuecomment-317035243  

Thanks. So, you've declared a value of uint32_t type, and you passed empty string to it, so when `store` is called, it tries to convert empty string to uint32_t and naturally fails. That seems an expected behavior, what were you expecting instead?

---

## Comment 4

> Username: thomas955  
> Created at: 2017-07-24 09:54:40 UTC  
> Url: https://github.com/boostorg/program_options/issues/30#issuecomment-317374785  

But the problem is that the program aborts. An exception is thrown, which is expected, but the what() call on the exception instance throws another exception. Note that according to cppreference, std::exception::what() is noexcept since C++11.

---

## Comment 5

> Username: vprus  
> Created at: 2017-07-24 09:55:54 UTC  
> Url: https://github.com/boostorg/program_options/issues/30#issuecomment-317375061  

That sound like https://github.com/boostorg/program_options/issues/31, which is fixed in develop. Can you give that a try?

---

## Comment 6

> Username: vprus  
> Created at: 2017-07-24 18:50:01 UTC  
> Url: https://github.com/boostorg/program_options/issues/30#issuecomment-317518963  

I have added a test for the usecase you have provided, which fails prior to fix of #31, and passes after, so yes, that was the same issue. Thanks for the report, the fix is merged to 1.65.0

---

## Comment 7

> Username: thomas955  
> Created at: 2017-07-25 13:37:54 UTC  
> Url: https://github.com/boostorg/program_options/issues/30#issuecomment-317739767  

Can confirm this, thank you!
