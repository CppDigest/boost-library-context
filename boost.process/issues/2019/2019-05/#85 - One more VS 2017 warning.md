# #85 - One more VS 2017 warning [Open]

> Username: goodguysoft  
> Created at: 2019-05-30 09:57:25 UTC  
> Updated at: 2019-06-26 09:24:30 UTC  
> Url: https://github.com/boostorg/process/issues/85  

c:\include\boost\process\detail\windows\wait_group.hpp(30): warning C4244: 'argument': conversion from 'std::chrono::system_clock::rep' to 'boost::winapi::DWORD_', possible loss of data  
  
Please apply trivial fix to avoid the warning, because fake compile time warnings may sidetrack attention from really dangerous warnings.

---

## Comment 1

> Username: pitrou  
> Created at: 2019-06-26 09:24:30 UTC  
> Url: https://github.com/boostorg/process/issues/85#issuecomment-505795712  

And it can also fail builds when warnings-as-errors is enabled.
