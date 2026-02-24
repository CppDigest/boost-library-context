# #148 - error: debug_output_backend is not a member of sinks [Closed]

> Username: RotateAt60MPH  
> Created at: 2021-04-02 00:53:36 UTC  
> Updated at: 2021-04-02 08:15:40 UTC  
> Closed at: 2021-04-02 08:14:08 UTC  
> Url: https://github.com/boostorg/log/issues/148  

I'm trying to compile a x64-linux project under CentOS7 that has the following typedef in an #include (SystemConfiguration.h) file:  
  
```  
typedef sinks::synchronous_sink<sinks::debug_output_backend> sink_t;  
```  
  
I get the following compile error:  
  
```  
/home/Devel/Cores/generator/lib/Common/../Common/SystemConfiguration.h:130:40: error: âdebug_output_backendâ is not a member of âsinksâ  
  130 | typedef sinks::synchronous_sink<sinks::debug_output_backend> sink_t;  
      |                                        ^~~~~~~~~~~~~~~~~~~~  
/home/Devel/Cores/generator/lib/Common/../Common/SystemConfiguration.h:130:60: error: template argument 1 is invalid  
  130 | typedef sinks::synchronous_sink<sinks::debug_output_backend> sink_t;  
      |                                                            ^  
  
```  
  
Boost::Log (1.75.0#1) came in as part of a vcpkg Boost package in both environments.  The identical code compiles and links fine under x64 Windows/Visual Studio 2019.  Both environment are using CMAKE with no explicit #defines that might affect Boost::Log that I can see.  On CentOS, I'm using gcc 9.3.1 to get some C++20 features.  
  
Any advice appreciated.

---

## Comment 1

> Username: Lastique  
> Created at: 2021-04-02 08:14:08 UTC  
> Updated at: 2021-04-02 08:15:40 UTC  
> Url: https://github.com/boostorg/log/issues/148#issuecomment-812406721  

`debug_output_backend` is [Windows-specific](https://www.boost.org/doc/libs/1_75_0/libs/log/doc/html/log/detailed/sink_backends.html#log.detailed.sink_backends.debugger), it is not available on other systems.
