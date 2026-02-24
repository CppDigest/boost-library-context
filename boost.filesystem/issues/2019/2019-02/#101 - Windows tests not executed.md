# #101 - Windows tests not executed [Closed]

> Username: Flamefire  
> Created at: 2019-02-04 09:33:33 UTC  
> Updated at: 2019-02-04 10:49:31 UTC  
> Closed at: 2019-02-04 10:46:20 UTC  
> Url: https://github.com/boostorg/filesystem/issues/101  

The windows-only tests are not executed since it checks `BOOST_PLATFORM` against `"Windows"` but that definition is defined to `"Win32"` instead: https://github.com/boostorg/config/blob/c2b1df496ab856921316ec29ddfe7d11013594fb/include/boost/config/platform/win32.hpp#L13  
  
This affects e.g. https://github.com/boostorg/filesystem/blob/b0a0fde6509df970a5387ce8d3ae0d47846f0907/test/operations_test.cpp#L317  
  
but other files I found using grep include:   
  
```  
example/directory_symlink_parent_resolution.cpp  
test/deprecated_test.cpp  
test/operations_test.cpp  
test/path_test.cpp  
test/issues/recurse_dir_iter_5403.cpp  
```  
  
To avoid this I'd suggest using the Jamfile mechanism to determine whether to execute the platform specific tests **if** that provides checking against invalid platforms(/`target-os`)

---

## Comment 1

> Username: Lastique  
> Created at: 2019-02-04 10:46:20 UTC  
> Updated at: 2019-02-04 10:49:31 UTC  
> Url: https://github.com/boostorg/filesystem/issues/101#issuecomment-460202966  

The `platform` variable does not actually contain `BOOST_PLATFORM`, see:  
  
https://github.com/boostorg/filesystem/blob/b0a0fde6509df970a5387ce8d3ae0d47846f0907/test/operations_test.cpp#L2206-L2220
