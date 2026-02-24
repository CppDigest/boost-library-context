# #73 - basic_command_line_parser::options() may store address of temporary [Open]

> Username: markand  
> Created at: 2019-01-28 15:38:13 UTC  
> Updated at: 2019-01-28 15:38:13 UTC  
> Url: https://github.com/boostorg/program_options/issues/73  

Hi,  
  
I've stambled accross a bug in my application about unrecognized options. After several minutes and checks I've realized it comes from Boost Program_Options because the `basic_command_line_parser::options` function store the address of a const-reference passed as argument. And since temporaries can bind to const-reference the library did use a dangling pointer.  
  
It is not mentioned in the [documentation](https://www.boost.org/doc/libs/1_69_0/doc/html/boost/program_options/basic_command_line_parser.html) that user must specify a valid reference.  
  
I propose that options() and positional() make a copy (as it's backward compatible) or to take a non-const reference as it explains correctly the ownership to the caller (but not backward compatible)  
  
Example of code that may reproduce the bug:  
  
```cpp  
#include <boost/program_options.hpp>  
  
namespace po = boost::program_options;  
  
po::options_description create()  
{  
	po::options_description desc;  
  
	desc.add_options()  
		("verbose", po::bool_switch());  
  
	return desc;  
}  
  
int main(int argc, char** argv)  
{  
	po::variables_map vm;  
	po::store(po::command_line_parser(argc, argv).options(create()).run(), vm);  
	po::notify(vm);  
}  
```  
  
Offending code https://github.com/boostorg/program_options/blob/develop/include/boost/program_options/detail/parsers.hpp#L37
