# #76 Switch to std::error_code from boost::system::error_code where possible [Merged]

> Username: apolukhin  
> Created at: 2024-12-15 17:41:41 UTC  
> Updated at: 2024-12-17 09:07:43 UTC  
> Merged at: 2024-12-17 09:07:39 UTC  
> Closed at: 2024-12-17 09:07:39 UTC  
> Url: https://github.com/boostorg/dll/pull/76  



---

## Review 1 [Commented]

> Username: apolukhin  
> Created_at: 2024-12-16 08:03:46 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/dll/pull/76#pullrequestreview-2505414579  

📁 test/cpp_load_test.cpp

```diff
 137 |         sm.get_mem_fn<override_class, int()>("get");
 138 |         BOOST_TEST(false);
 139 |-     } catch(boost::dll::fs::system_error &) {}
```

> Username: apolukhin  
> Created_at: 2024-12-16 08:03:45 UTC  
> Updated_at: 2024-12-16 08:03:46 UTC  
> Url: https://github.com/boostorg/dll/pull/76#discussion_r1886339858  

TODO: this may break user code. I have to keep throwing boost::system::error_code


---
