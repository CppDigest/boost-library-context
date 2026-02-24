# #99 - Duplicate option not detected when added [Open]

> Username: pgerell  
> Created at: 2020-10-18 22:48:33 UTC  
> Updated at: 2020-10-18 22:48:33 UTC  
> Url: https://github.com/boostorg/program_options/issues/99  

According to the documentation, `options_description::add` should throw `duplicate_variable_error`.  
This is not the case. Instead `ambiguous_option` is thrown when parsing the command line.  
Ironically from within a function called `find_nothrow`  
  
Many of the tests in https://github.com/boostorg/program_options/blob/develop/test/exception_test.cpp should throw `duplicate_variabl_error`
