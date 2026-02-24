# #102 - canonical_name option for error_with_option_name is giving NULL string [Open]

> Username: sgoru  
> Created at: 2020-10-26 05:23:37 UTC  
> Updated at: 2020-11-04 11:10:04 UTC  
> Url: https://github.com/boostorg/program_options/issues/102  

The error is "option is ambiguous and matches 'parent_run' and 'parent_hierarchy' " instead it should be "option '-parent' is ambiguous and matches '-parent_run' and '-parent_hierarchy' " Here since -parent is canonical name and it set to NULL string , it is giving the first error message. I have checked the canonical name using get_canonical_name function.   
Below is the code snippet.  
catch(boost::program_options::error& e){  
  std::cout<<e.what();  
}
