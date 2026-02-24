# #459 improve docs( example/cpp11/server.cpp ) [Open]

> Username: keeAzlynth  
> Created at: 2025-10-20 14:50:43 UTC  
> Updated at: 2025-12-01 08:53:02 UTC  
> Url: https://github.com/boostorg/asio/pull/459  

The source code in this repository is generated from an upstream repository at https://github.com/chriskohlhoff/asio.  
## For historical reasons, the old examples are commented out (not deleted) so that other readers can still reference them.  
  
### Rationale  
- API Compatibility: The io_context allocator constructor was removed in Boost 1.87+ but examples still reference it  
  
- Modern C++ Standards: std::aligned_storage is deprecated in C++23  
  
- Best Practices: Handler-level allocation remains the recommended approach for performance optimization  
  
- User Experience: Prevents compilation errors and confusion for users following official examples  
  
### Testing  
- Verified examples compile with Boost 1.89+  
  
- Confirmed compatibility with C++17, C++20, and upcoming C++23 standards  
  
- Ensured handler allocation functionality remains intact  
  
  
  
Please consid  
<img width="1404" height="439" alt="屏幕截图 2025-10-20 222351" src="https://github.com/user-attachments/assets/76b512e1-fb29-4afb-a24a-9ca28d1c7873" />  
er raising new pull requests at https://github.com/chriskohlhoff/asio/pulls.

---

## Comment 1

> Username: keeAzlynth  
> Created_at: 2025-10-20 14:57:03 UTC  
> Url: https://github.com/boostorg/asio/pull/459#issuecomment-3422450354  

Sorry about this information  
Related Issues  
Boost version compatibility notes  
  
C++23 deprecation of std::aligned_storage  
  
Documentation synchronization with API changes

---

## Comment 2

> Username: nigels-com  
> Created_at: 2025-12-01 08:53:02 UTC  
> Url: https://github.com/boostorg/asio/pull/459#issuecomment-3595337012  

Perhaps a C++20 or C++23 version added, rather than modifying the "works just fine on C++11" one.

---
