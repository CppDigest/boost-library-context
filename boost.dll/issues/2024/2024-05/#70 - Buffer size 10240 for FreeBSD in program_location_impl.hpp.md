# #70 - Buffer size 10240 for FreeBSD in program_location_impl.hpp [Closed]

> Username: darealshinji  
> Created at: 2024-05-14 01:07:12 UTC  
> Updated at: 2024-12-21 12:10:31 UTC  
> Closed at: 2024-12-21 12:09:53 UTC  
> Url: https://github.com/boostorg/dll/issues/70  

In `boost/dll/detail/posix/program_location_impl.hpp` for FreeBSD the buffer size is 10240. Is that correct or a typo and it should be 1024?  
  
https://github.com/boostorg/dll/blob/6c60dde50bf67138c90cc84938111866813feaff/include/boost/dll/detail/posix/program_location_impl.hpp#L71

---

## Comment 1

> Username: apolukhin  
> Created at: 2024-12-21 12:10:29 UTC  
> Url: https://github.com/boostorg/dll/issues/70#issuecomment-2558098717  

Thanks for the report, changed the function logic!
