# #84 - variable-sized queue with a custom allocator is not constructible [Closed]

> Username: zlojvavan  
> Created at: 2023-05-05 09:19:05 UTC  
> Updated at: 2023-11-03 13:33:11 UTC  
> Closed at: 2023-11-03 13:33:11 UTC  
> Url: https://github.com/boostorg/lockfree/issues/84  

due to template param deduction failure in constructor declaration (line 249), see [godbolt](https://godbolt.org/z/GEYY56cb4)  
  
as a quick and dirty workaround I replaced "typename boost::allocator_rebind<node_allocator, U>::type const& alloc" with plain "U const &alloc" there in C:\Project\Libs\vcpkg\installed\x64-windows-static\include\boost\lockfree\queue.hpp and that made it compile and work but I hope it's going to be fixed in the trunk

---

## Comment 1

> Username: zlojvavan  
> Created at: 2023-11-03 10:53:36 UTC  
> Url: https://github.com/boostorg/lockfree/issues/84#issuecomment-1792232810  

@timblechmann thank you
