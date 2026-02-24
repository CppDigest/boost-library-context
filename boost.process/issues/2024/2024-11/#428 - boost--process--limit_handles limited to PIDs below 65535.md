# #428 - boost::process::limit_handles limited to PIDs below 65535 [Open]

> Username: Breakwell  
> Created at: 2024-11-08 15:42:16 UTC  
> Updated at: 2024-11-08 15:44:33 UTC  
> Url: https://github.com/boostorg/process/issues/428  

The call to [get_handles](https://github.com/boostorg/process/blob/58446b502a5fc2680789bb41b69dba74bb9230e6/include/boost/process/v1/detail/windows/handles.hpp#L136) is limited to PID sizes of up to 65535 (USHRT_MAX). This is because of [this](https://github.com/boostorg/process/blob/58446b502a5fc2680789bb41b69dba74bb9230e6/include/boost/process/v1/detail/windows/handles.hpp#L41) call to NtQuerySystemInformation.  
  
As it's undocumented it's hard to hunt down but it looks like the NtQuerySystemInformation call is limited to PIDs in the range of an unsigned short. To fix this instead of SYSTEM_HANDLE_INFORMATION we would need to consider using SYSTEM_HANDLE_INFORMATION_EX to query the system handles.  
  
I think it would be safer to possibly use UpdateProcThreadAttribute() (at least for vista +) as suggested [originally](https://github.com/klemens-morgenstern/boost-process/pull/196/files#r280065110). It seems a bit unnecessary to iterate over all the system handles in order to limit the handles inherited by a process when UpdateProcThreadAttribute() is supported.

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2024-11-08 15:44:32 UTC  
> Url: https://github.com/boostorg/process/issues/428#issuecomment-2465078827  

There's a PR for adding this to v2: https://github.com/boostorg/process/pull/423  
  
I'll deprecate v1 in the upcoming release, so I don't know if I'll add this.
