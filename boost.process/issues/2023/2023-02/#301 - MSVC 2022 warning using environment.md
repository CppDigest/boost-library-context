# #301 - MSVC 2022 warning using environment [Closed]

> Username: fsmoke  
> Created at: 2023-02-17 16:42:37 UTC  
> Updated at: 2023-06-28 12:24:18 UTC  
> Closed at: 2023-06-28 12:24:17 UTC  
> Url: https://github.com/boostorg/process/issues/301  

When i use environment stuff i'ev got warnings  
  
my code   
  
```  
int main(int argc, char * argv[])  
{  
	namespace bp = boost::process;  
  
	auto env = boost::this_process::environment();  
	bp::environment new_env = env;  
	std::vector<std::string> args;  
	std::copy(argv + 1, argv + argc, std::back_inserter(args));  
  
	boost::filesystem::path initial = argv[0];  
	initial.remove_filename();  
  
	std::vector<std::string> paths_vec;  
	boost::split(paths_vec, std::string(start_info::paths), [](char_t c)->bool { return c == ';'; },  
		boost::algorithm::token_compress_on);  
  
	std::stringstream rebuilded_paths;  
	std::string delim;  
	for (auto const & p : paths_vec)  
	{  
		boost::filesystem::path path = p;  
		rebuilded_paths << delim;  
		if (!path.is_absolute())  
			path = initial / path;  
  
		rebuilded_paths << path.string();  
		delim = ";";  
	}  
  
	for (auto const & e : new_env)  
	{  
		if (boost::iequals(e.get_name(), "path"))  
		{  
			new_env[e.get_name()] += rebuilded_paths.str();  
			break;  
		}  
	}  
	  
	return bp::system(initial / boost::filesystem::path(start_info::filename), args, new_env);  
}  
```  
  
piece of build log:  
  
```  
D:\libraries\boost_1_80_0\boost\process\environment.hpp(266,1): warning C4267: 'initializing': conversion from 'size_t' to 'int', possible loss of data  
  D:\libraries\boost_1_80_0\boost\process\environment.hpp(261,1): message : while compiling class template member function 'boost::iterators::transform_iterator<boost::process::detail::make_entry<Char,boost::process::basic_environment_impl<Char,boost::process::detail::windows::basic_environment_impl>>,Char **,boost::process::detail::entry<Char,boost::process::basic_environment_impl<Char,boost::process::detail::windows::basic_environment_impl>>,boost::process::detail::entry<Char,boost::process::basic_environment_impl<Char,boost::process::detail::windows::basic_environment_impl>>> boost::process::basic_environment_impl<Char,boost::process::detail::windows::basic_environment_impl>::find(const std::basic_string<char,std::char_traits<char>,std::allocator<char>> &)'  
          with  
          [  
              Char=char  
          ]  
  D:\libraries\boost_1_80_0\boost\process\environment.hpp(361,14): message : see reference to function template instantiation 'boost::iterators::transform_iterator<boost::process::detail::make_entry<Char,boost::process::basic_environment_impl<Char,boost::process::detail::windows::basic_environment_impl>>,Char **,boost::process::detail::entry<Char,boost::process::basic_environment_impl<Char,boost::process::detail::windows::basic_environment_impl>>,boost::process::detail::entry<Char,boost::process::basic_environment_impl<Char,boost::process::detail::windows::basic_environment_impl>>> boost::process::basic_environment_impl<Char,boost::process::detail::windows::basic_environment_impl>::find(const std::basic_string<char,std::char_traits<char>,std::allocator<char>> &)' being compiled  
          with  
          [  
              Char=char  
          ]  
  D:\libraries\boost_1_80_0\boost\process\environment.hpp(632,1): message : see reference to class template instantiation 'boost::process::basic_environment_impl<Char,boost::process::detail::windows::basic_environment_impl>' being compiled  
          with  
          [  
              Char=char  
          ]  
  D:\libraries\boost_1_80_0\boost\process\env.hpp(176,1): message : see reference to class template instantiation 'boost::process::basic_environment<char>' being compiled  
  D:\libraries\boost_1_80_0\boost\process\env.hpp(183,1): message : see reference to class template instantiation 'boost::process::detail::env_init<char>' being compiled  
  D:\libraries\boost_1_80_0\boost\process\environment.hpp(266,1): warning C4267: 'initializing': conversion from 'size_t' to 'const int', possible loss of data  
```

---

## Comment 1

> Username: fsmoke  
> Created at: 2023-05-26 10:21:18 UTC  
> Url: https://github.com/boostorg/process/issues/301#issuecomment-1564168104  

What about this issue? I don't think that's hard to fix....
