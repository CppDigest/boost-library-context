# #516 - linker error with BOOST_PROCESS_USE_STD_FS when using process_environment [Closed]

> Username: pgit  
> Created at: 2025-09-21 18:57:34 UTC  
> Updated at: 2025-10-06 05:17:21 UTC  
> Closed at: 2025-10-06 05:17:21 UTC  
> Url: https://github.com/boostorg/process/issues/516  

When using `boost::process:v2` with a `process_environment` for passing environment, there is a linker error about `on_setup(..., std::filesystem::path, ...)` when `BOOST_PROCESS_USE_STD_FS` is defined: https://godbolt.org/z/azbrYesMj  
  
```  
undefined reference to `boost::process::v2::process_environment::on_setup(boost::process::v2::posix::default_launcher&, std::filesystem::__cxx11::path const&, char const* const*)  
```  
  
When re-compiling Boost with `BOOST_PROCESS_USE_STD_FS`, it works again. Now this isn't really surprising, as there are two different `on_setup` versions, one using `boost::filesystem` and another one using `std::filesystem`.: The pre-compiled `boost_process` library defined and exported `on_setup` differently than my code using BOOST_PROCESS_USE_STD_FS.  
  
Is there maybe a way to put that function into a header file, or export both variants?
