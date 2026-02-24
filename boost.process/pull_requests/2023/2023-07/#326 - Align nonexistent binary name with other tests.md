# #326 Align nonexistent binary name with other tests [Closed]

> Username: SilverPlate3  
> Created at: 2023-07-02 19:23:18 UTC  
> Updated at: 2023-07-02 19:28:14 UTC  
> Closed at: 2023-07-02 19:27:52 UTC  
> Url: https://github.com/boostorg/process/pull/326  

All the other tests that try to run a nonexistent binary use the name "doesnt-exist", which is a much more logical  name to this purpose then "doesnt-exit".  
For example: https://github.com/boostorg/process/blob/bfb1ebb5bd24837b6292421dba3790f5701883ba/test/posix_specific.cpp#L96

---
