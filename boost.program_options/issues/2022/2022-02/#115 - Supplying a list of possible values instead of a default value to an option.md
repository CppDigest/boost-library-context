# #115 - Supplying a list of possible values instead of a default value to an option? [Open]

> Username: IsabelMarleen  
> Created at: 2022-02-14 22:28:10 UTC  
> Updated at: 2022-02-14 22:28:10 UTC  
> Url: https://github.com/boostorg/program_options/issues/115  

Is there a way to specify a restricted list of values a command line option can take? I was thinking of something like this:  
```  
#include <boost/program_options.hpp>  
namespace po = boost::program_options;  
  
po::options_description description("General options");  
description.add_options()  
       ("key", po::value<string>()->possible_values("a", "b", "c"), "description of key");  
```  
Running `executable --key d` in the command line would throw an error but `executable --key a` would not. I know there is the option of manually processing the argument supplied to the key later on, but I was wondering if the functionality of identifying unambiguous abbreviations could be utilised.
