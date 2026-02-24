# #382 FIX: fix the incorrect parameter parsing [Closed]

> Username: zhixingchen0629  
> Created at: 2024-07-08 09:25:21 UTC  
> Updated at: 2024-12-03 03:21:20 UTC  
> Closed at: 2024-12-03 03:21:19 UTC  
> Url: https://github.com/boostorg/process/pull/382  

if i pass this params vector to launch process when the executor was nullptr:  
```cpp  
        std::string executor;  
        std::vector<std::string> params;  
        params.push_back("powershell");  
        params.push_back("-noprofile");  
        params.push_back("-executionpolicy");  
        params.push_back("bypass");  
        params.push_back("-file");  
        params.push_back(m_command);  
        auto launcher = boost::process::v2::default_process_launcher();  
        m_child = std::move(launcher(m_ioContext, ec, executor.c_str(), params, boost::process::v2::process_stdio{nullptr, m_wPipe, m_wPipe}));  
```  
The actual splicing command_line at the bottom level is:  
```  
"" powershell -noprofile -executionpolicy bypass -file c:aaa  
```  
  
`"" `It is superfluous, which cause create process error.  
  
parameter splicing should take into account the situation where executor is empty

---

## Comment 1

> Username: klemens-morgenstern  
> Created_at: 2024-10-24 23:48:18 UTC  
> Url: https://github.com/boostorg/process/pull/382#issuecomment-2436529092  

I assume you are trying to use CreateProcess with an empty lpApplicationName to get a command line execution?   
  
This is disabled by default, because it's security risk and not portable. They way to do this is:  
  
```cpp  
        auto executor = boost::process::v2::find_executable("powershell");  
        std::vector<std::string> params;  
        params.push_back("-noprofile");  
        params.push_back("-executionpolicy");  
        params.push_back("bypass");  
        params.push_back("-file");  
        params.push_back(m_command);  
        auto launcher = boost::process::v2::default_process_launcher();  
        m_child = std::move(launcher(m_ioContext, ec, executor, params, boost::process::v2::process_stdio{nullptr, m_wPipe, m_wPipe}));  
  
```

---
