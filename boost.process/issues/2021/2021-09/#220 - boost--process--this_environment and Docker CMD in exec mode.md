# #220 - boost::process::this_environment and Docker CMD in exec mode [Open]

> Username: daminetreg  
> Created at: 2021-09-08 10:53:03 UTC  
> Updated at: 2021-09-08 10:53:03 UTC  
> Url: https://github.com/boostorg/process/issues/220  

Dear @klemens-morgenstern,  
  
This is more a question and info for workaround than an actual bugfix request, because I can't see an error within boost.process, but perhaps you think otherwise.  
  
We have seen a crash that is almost always reproducible, is when setting environment variable on the object returned by `boost::process::this_environment` when run inside a docker on linux.  
  
Given such a cpp code and a docker with version 20.10.7-0ubuntu1~20.04.1 (version of boost process is old here 1.73.0 but I don't think there has been changes on this part), built with clang 11 on linux (std c++17) and code :   
  
```cpp  
  class cli {  
    public:  
  
    cli() {  
      auto env = boost::this_process::environment();  
  
      env["AZURE_CORE_OUTPUT"] = "json";  
      env["AZURE_CORE_DISABLE_CONFIRM_PROMPT"] = "true";  
      env["AZURE_CORE_COLLECT_TELEMETRY"] = "false";  
      env["AZURE_CORE_NO_COLOR"] = "true";  
    }      
};  
```  
  
```dockerfile  
FROM ubuntu:20.04 as production  
  
#...snip...  
  
CMD [ "./tipibuild" ]  
```  
  
We get almost-always crashes of the following form:  
  
```  
 0# tipi::util::detail::on_terminate_print_stacktrace() in ./tipibuild,  
 1# tipi::util::detail::on_crash(int) in ./tipibuild,  
 2# 0x00007F4000B1E210 in /lib/x86_64-linux-gnu/libc.so.6,  
 3# 0x00007F4000C5FC5E in /lib/x86_64-linux-gnu/libc.so.6,  
 4# boost::process::basic_environment_impl<char, boost::process::detail::posix::native_environment_impl>::find(std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > const&) in ./tipibuild,  
 5# boost::process::basic_environment_impl<char, boost::process::detail::posix::native_environment_impl>::operator[](std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > const&) in ./tipibuild,  
 6# tipi::clouds::azure::cli::cli(tipi::clouds::azure::azure_config_t) in ./tipibuild,  
 7# tipi::clouds::azure_windows::azure_windows(tipi::build::service::Machines&, tipi::clouds::azure::azure_config_t) in ./tipibuild,  
 8# void __gnu_cxx::new_allocator<tipi::clouds::azure_windows>::construct<tipi::clouds::azure_windows, tipi::build::service::Machines&, tipi::clouds::azure::azure_config_t const&>(tipi::clouds::azure_windows*, tipi::build::service::Machines&, tipi::clouds::azure::azure_config_t const&) in ./tipibuild,  
 9# std::__shared_count<(__gnu_cxx::_Lock_policy)2>::__shared_count<tipi::clouds::azure_windows, std::allocator<tipi::clouds::azure_windows>, tipi::build::service::Machines&, tipi::clouds::azure::azure_config_t const&>(tipi::clouds::azure_windows*&, std::_Sp_alloc_shared_tag<std::allocator<tipi::clouds::azure_windows> >, tipi::build::service::Machines&, tipi::clouds::azure::azure_config_t const&) in ./tipibuild,  
10# tipi::build::service::CloudSelector::CloudSelector(tipi::build::service::Machines&) in ./tipibuild,  
11# default_tipi_app_components::default_tipi_app_components(boost::asio::io_context&) in ./tipibuild,  
12# tipibuild_t<default_tipi_app_components>::tipibuild_t(boost::asio::io_context&) in ./tipibuild,  
13# main in ./tipibuild,  
```  
  
Just before crashing the process tree looks likes this :  
![image](https://user-images.githubusercontent.com/181726/132496726-b619b405-e19c-4248-a18e-ba51e14b463e.png)  
  
  
  
## Workaround  
if you change the dockerfile to use the shell execution mode of docker instead of the exec mode :   
  
```dockerfile  
FROM ubuntu:20.04 as production  
  
#...snip...  
  
CMD "./tipibuild"  
```  
  
Thanks to the small shell process in between docker and the commands it just works fine :   
<img width="1003" alt="image" src="https://user-images.githubusercontent.com/181726/132495158-cf9aa0b7-f61b-4e19-bf87-63fd2d116f14.png">  
  
Do you think it can be a bug in Boost process or more like something wrong in linux containers ?
