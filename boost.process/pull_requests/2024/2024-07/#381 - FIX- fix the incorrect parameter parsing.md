# #381 FIX: fix the incorrect parameter parsing [Closed]

> Username: zhixingchen0629  
> Created at: 2024-07-08 09:24:32 UTC  
> Updated at: 2024-07-08 12:52:22 UTC  
> Closed at: 2024-07-08 09:24:56 UTC  
> Url: https://github.com/boostorg/process/pull/381  

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
> Created_at: 2024-07-08 11:13:18 UTC  
> Url: https://github.com/boostorg/process/pull/381#issuecomment-2213717289  

I am assuming that was fixed in develop already?

---

## Comment 2

> Username: zhixingchen0629  
> Created_at: 2024-07-08 12:50:17 UTC  
> Updated_at: 2024-07-08 12:52:22 UTC  
> Url: https://github.com/boostorg/process/pull/381#issuecomment-2213990872  

> assuming  
  
sorry, my pr was wrong commit, please check my latest pr   
https://github.com/boostorg/process/pull/382/

---
