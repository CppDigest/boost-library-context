# #83 - [Feature] Allow Unknown Options from all sources [Open]

> Username: joaomlneto  
> Created at: 2019-05-30 11:42:53 UTC  
> Updated at: 2019-09-21 06:59:14 UTC  
> Url: https://github.com/boostorg/program_options/issues/83  

For the command line parser, there is the [`allow_unregistered()` method](https://www.boost.org/doc/libs/1_70_0/doc/html/program_options/howto.html#id-1.3.32.6.9) to not throw an exception whenever an option is not recognized.  
  
However, this is missing from the configuration file and environment parsers.  
  
It is possible to do work around this for both [configuration files](https://stackoverflow.com/questions/23664503/parsing-unregistered-options-for-config-files-in-boost-program-options) and [environment](https://stackoverflow.com/questions/53285248/boost-program-options-allow-undeclared-from-environment), but I am of the opinion this should be handled in similar fashion to the command line parsers!

---

## Comment 1

> Username: ohois  
> Created at: 2019-09-21 06:59:14 UTC  
> Url: https://github.com/boostorg/program_options/issues/83#issuecomment-533774389  

This feature exists. The third parameter for parse_config_file   
  
https://github.com/boostorg/program_options/blob/develop/src/parsers.cpp#L101  
  
Haven't tested with environment but I guess it already ignores unregistered variables.
