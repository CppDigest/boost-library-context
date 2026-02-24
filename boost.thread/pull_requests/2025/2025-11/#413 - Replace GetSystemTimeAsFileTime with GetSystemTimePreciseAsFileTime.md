# #413 Replace GetSystemTimeAsFileTime with GetSystemTimePreciseAsFileTime [Open]

> Username: pgawro  
> Created at: 2025-11-02 11:53:29 UTC  
> Updated at: 2025-11-02 11:53:29 UTC  
> Url: https://github.com/boostorg/thread/pull/413  

According to Microsoft documentation this variant of GetSystemTime guarantiees to get highest posible level of precision (<1us). https://learn.microsoft.com/en-us/windows/win32/api/sysinfoapi/nf-sysinfoapi-getsystemtimepreciseasfiletime

---
