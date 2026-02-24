# #211 - Test sub_launcher.cpp is failed with no member named 'sleep_for' in namespace 'std::this_thread' in gcc 11 [Open]

> Username: yuxianch  
> Created at: 2021-06-21 07:33:52 UTC  
> Updated at: 2021-06-21 07:34:12 UTC  
> Url: https://github.com/boostorg/process/issues/211  

Test sub_launcher.cpp is failed with no member named 'sleep_for' in namespace 'std::this_thread' in gcc 11.   
Need to include \<thread\> explicitly.  
https://github.com/boostorg/process/blob/741d3f4a07a65736c7ce60ea2f1084f079edec5e/test/sub_launcher.cpp#L79
