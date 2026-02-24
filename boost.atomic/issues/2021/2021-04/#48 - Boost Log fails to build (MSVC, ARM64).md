# #48 - Boost Log fails to build (MSVC, ARM64) [Closed]

> Username: juliekoubova  
> Created at: 2021-04-01 15:43:28 UTC  
> Updated at: 2021-04-01 16:48:41 UTC  
> Closed at: 2021-04-01 16:48:41 UTC  
> Url: https://github.com/boostorg/atomic/issues/48  

Hi, I might be doing something stupid, but my app fails to build for ARM64 when using Boost Log. The problem is in the [core_arch_ops_msvc_arm.hpp header](https://github.com/boostorg/atomic/blob/develop/include/boost/atomic/detail/core_arch_ops_msvc_arm.hpp#L225) in all the places where the derived class tries to call the `cas_common_order` method from the base class. That method fails to resolve because the base class is a template:  
```  
...\vcpkg_installed\ARM64-windows\include\boost\atomic\detail\core_arch_ops_msvc_arm.hpp(225,17): error C3861: 'cas_common_order': identifier not found  
...\vcpkg_installed\ARM64-windows\include\boost\atomic\detail\core_arch_ops_msvc_arm.hpp(225,33): message : 'cas_common_order': function declaration must be available as none of the arguments depend on a template parameter  
...\vcpkg_installed\ARM64-windows\include\boost\atomic\detail\core_arch_ops_msvc_arm.hpp(316): message : see reference to class template instantiation 'boost::atomics::detail::core_arch_operations<1,Signed,Interprocess>' being compiled  
```

---

## Comment 1

> Username: Lastique  
> Created at: 2021-04-01 16:48:41 UTC  
> Url: https://github.com/boostorg/atomic/issues/48#issuecomment-812036178  

Fixed by https://github.com/boostorg/atomic/pull/49.
