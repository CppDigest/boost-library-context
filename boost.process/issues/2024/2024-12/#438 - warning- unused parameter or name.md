# #438 - warning: unused parameter or name [Open]

> Username: marakew  
> Created at: 2024-12-09 12:52:54 UTC  
> Updated at: 2024-12-09 12:52:54 UTC  
> Url: https://github.com/boostorg/process/issues/438  

would be nice to fix this warn  
  
```  
In file included from D:\git\boost_\libs\process\include\boost/process/v1/io.hpp:14:  
D:\git\boost_\libs\process\include\boost/process/v1/pipe.hpp(399,35): warning: unused parameter 'name' [-Wunused-parameter]  
  399 |     void open(const std::string & name)  
      |                                   ^  
D:\git\boost_\libs\process\include\boost/process/v1/pipe.hpp(507,35): warning: unused parameter 'name' [-Wunused-parameter]  
  507 |     void open(const std::string & name)  
      |                                   ^  
D:\git\boost_\libs\process\include\boost/process/v1/pipe.hpp(615,35): warning: unused parameter 'name' [-Wunused-parameter]  
  615 |     void open(const std::string & name)  
      |                                   ^  
```  
  
```  
D:\git\boost_\libs\process\include\boost/process/v1/detail/windows/handles.hpp(141,45): warning: unused variable 'flags' [-Wunused-variable]  
  141 |                     ::boost::winapi::DWORD_ flags = 0u;  
      |                                             ^~~~~  
D:\git\boost_\libs\process\include\boost/process/v1/detail/windows/handles.hpp(157,30): warning: unused parameter 'exec' [-Wunused-parameter]  
  157 |     void on_error(Executor & exec, const std::error_code & ec) const  
      |                              ^  
D:\git\boost_\libs\process\include\boost/process/v1/detail/windows/handles.hpp(157,60): warning: unused parameter 'ec' [-Wunused-parameter]  
  157 |     void on_error(Executor & exec, const std::error_code & ec) const  
      |                                                            ^  
D:\git\boost_\libs\process\include\boost/process/v1/detail/windows/handles.hpp(164,32): warning: unused parameter 'exec' [-Wunused-parameter]  
  164 |     void on_success(Executor & exec) const  
      |                                ^  
  
```
