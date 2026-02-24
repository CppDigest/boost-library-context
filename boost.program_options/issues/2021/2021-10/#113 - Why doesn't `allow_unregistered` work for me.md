# #113 - Why doesn't `allow_unregistered` work for me? [Open]

> Username: KOLANICH  
> Created at: 2021-10-20 10:51:06 UTC  
> Updated at: 2021-10-20 10:52:21 UTC  
> Url: https://github.com/boostorg/program_options/issues/113  

I still get `too many positional options have been specified on the command line` on `run()`  
  
The excerpt  
  
```c++  
boost::program_options::variables_map vm;  
try{  
	auto parsed = boost::program_options::command_line_parser(rawArgs.argc, rawArgs.argv).options(oDesc).positional(pd).allow_unregistered().run();  //too many positional options have been specified on the command line  
	boost::program_options::store(parsed, vm);  
	boost::program_options::notify(vm);  
	auto rest = boost::program_options::collect_unrecognized(parsed.options, boost::program_options::collect_unrecognized_mode::include_positional);  
} catch(std::exception &ex) {  
	PROCESS_CASE_OF_SYNTAX_ERROR_WHATABLE_EXCEPTION(ex);  
}  
```  
  
The full source is here: https://github.com/HydrArgs/HydrArgs/blob/master/backends/BoostArgs.cpp
