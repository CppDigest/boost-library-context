# #78 - Long option name tokens are parsed as previous option value [Open]

> Username: dwielosz  
> Created at: 2019-04-19 14:07:15 UTC  
> Updated at: 2019-04-19 14:07:15 UTC  
> Url: https://github.com/boostorg/program_options/issues/78  

https://github.com/boostorg/program_options/blob/d95d31684832075fda04c9fc916f8ec875552763/src/cmdline.cpp#L477  
  
If long option follows an option with (string) value, it is parsed as value instead of known option. For example in command line "--option_with_value --known_option", option_with_value will have value equal to "--known_option". This issue does not occur if following option is in short format.  
  
As I understand the code, find_nothrow in referenced file is given token with leading dashes instead of option's string_key (or name).
