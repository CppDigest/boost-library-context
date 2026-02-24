# #159 - Compile error when including <boost/range/any_range.hpp> without <boost/type_traits.hpp> [Closed]

> Username: adentinger  
> Created at: 2025-10-09 21:08:47 UTC  
> Updated at: 2025-10-09 22:10:24 UTC  
> Closed at: 2025-10-09 21:59:04 UTC  
> Url: https://github.com/boostorg/range/issues/159  

- With: Ubuntu  
- Compiler: GCC 9.4.0  
- Boost version: 1.89 (installed via vcpkg)  
  
## To reproduce  
Just include `<boost/range/any_range.hpp>` in any TU that hasn't included `<boost/type_traits.hpp>` first. This produces the following compile errors:  
  
```  
/usr/bin/c++  -isystem /home/anthonyd973/Desktop/foo/build/vcpkg_installed/x64-linux/include -std=gnu++17 -MD -MT CMakeFiles/foo.dir/main.cpp.o -MF CMakeFiles/foo.dir/main.cpp.o.d -o CMakeFiles/foo.dir/main.cpp.o -c /home/anthonyd973/Desktop/foo/main.cpp  
In file included from /home/anthonyd973/Desktop/foo/build/vcpkg_installed/x64-linux/include/boost/range/detail/any_iterator.hpp:22,  
                 from /home/anthonyd973/Desktop/foo/build/vcpkg_installed/x64-linux/include/boost/range/any_range.hpp:19,  
                 from /home/anthonyd973/Desktop/foo/main.cpp:1:  
/home/anthonyd973/Desktop/foo/build/vcpkg_installed/x64-linux/include/boost/range/detail/any_iterator_interface.hpp:34:13: error: template argument 2 is invalid  
   34 |             >::type type;  
      |             ^  
/home/anthonyd973/Desktop/foo/build/vcpkg_installed/x64-linux/include/boost/range/detail/any_iterator_interface.hpp:34:14: error: expected identifier before ‘::’ token  
   34 |             >::type type;  
      |              ^~  
/home/anthonyd973/Desktop/foo/build/vcpkg_installed/x64-linux/include/boost/range/detail/any_iterator_interface.hpp:34:16: error: typedef name may not be a nested-name-specifier  
   34 |             >::type type;  
      |                ^~~~  
/home/anthonyd973/Desktop/foo/build/vcpkg_installed/x64-linux/include/boost/range/detail/any_iterator_interface.hpp:34:16: error: expected ‘;’ at end of member declaration  
   34 |             >::type type;  
      |                ^~~~  
      |                    ;  
/home/anthonyd973/Desktop/foo/build/vcpkg_installed/x64-linux/include/boost/range/detail/any_iterator_interface.hpp:34:21: error: declaration does not declare anything [-fpermissive]  
   34 |             >::type type;  
      |                     ^~~~  
ninja: build stopped: subcommand failed.  
```

---

## Comment 1

> Username: adentinger  
> Created at: 2025-10-09 21:59:04 UTC  
> Updated at: 2025-10-09 22:10:24 UTC  
> Url: https://github.com/boostorg/range/issues/159#issuecomment-3387608523  

Closing this issue; after investigating it's caused by a missing include in `any_iterator_interface.hpp` that's since been fixed by #157.
