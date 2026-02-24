# #61 - `launder_cast` triggers warning about dropping `const` and `volatile` qualifiers [Closed]

> Username: lynqagearchitect  
> Created at: 2025-08-07 02:20:04 UTC  
> Updated at: 2025-11-02 20:48:59 UTC  
> Closed at: 2025-11-02 20:48:59 UTC  
> Url: https://github.com/boostorg/move/issues/61  

I'm using Boost 1.88.0 on my Mac, and I get the following error when trying to use `boost::container::static_vector`:  
  
```  
In file included from ./src/my_source_file.cc:8:  
In file included from ./inc/my_header_file.hh:12:  
In file included from /opt/homebrew/Cellar/boost/1.88.0/include/boost/container/static_vector.hpp:25:  
/opt/homebrew/Cellar/boost/1.88.0/include/boost/move/detail/launder.hpp:47:24: error: cast from 'const volatile void *' to 'MyClassName *' drops const and volatile qualifiers [-Werror,-Wcast-qual]  
   47 |    return (launder)((T)p);  
      |                        ^  
/opt/homebrew/Cellar/boost/1.88.0/include/boost/container/static_vector.hpp:67:27: note: in instantiation of function template specialization 'boost::move_detail::launder_cast<MyClassName *>' requested here  
   67 |    {  return move_detail::launder_cast<T*>(&storage);  }  
      |                           ^  
/opt/homebrew/Cellar/boost/1.88.0/include/boost/container/vector.hpp:736:30: note: in instantiation of member function 'boost::container::dtl::static_storage_allocator<MyClassName, 4, 0, true>::internal_storage' requested here  
  736 |    {  return allocator_type::internal_storage();  }  
      |                              ^  
/opt/homebrew/Cellar/boost/1.88.0/include/boost/container/vector.hpp:3225:54: note: in instantiation of member function 'boost::container::vector_alloc_holder<boost::container::dtl::static_storage_allocator<MyClassName, 4, 0, true>, unsigned long>::start' requested here  
 3225 |    {  return boost::movelib::to_raw_pointer(m_holder.start());  }  
      |                                                      ^  
/opt/homebrew/Cellar/boost/1.88.0/include/boost/container/vector.hpp:1216:47: note: in instantiation of member function 'boost::container::vector<MyClassName, boost::container::dtl::static_storage_allocator<MyClassName, 4, 0, true>>::priv_raw_begin' requested here  
 1216 |          (this->get_stored_allocator(), this->priv_raw_begin(), this->m_holder.m_size);  
      |                                               ^  
/opt/homebrew/Cellar/boost/1.88.0/include/boost/container/static_vector.hpp:162:7: note: in instantiation of member function 'boost::container::vector<MyClassName, boost::container::dtl::static_storage_allocator<MyClassName, 4, 0, true>>::~vector' requested here  
  162 | class static_vector  
      |       ^  
1 error generated.  
```  
  
I traced it to this bit of code in `move/detail/launder.hpp`:  
  
```  
template<class T>  
BOOST_MOVE_FORCEINLINE T launder_cast(const volatile void* p)  
{  
   return (launder)((T)p);  
}  
```  
  
I was able to work around it for now by adding a `const_cast<void*>`.  For good measure, I converted the remainder of the conversion to a `static_cast<T>`, to avoid any new warnings about C-style casts.  
  
```  
template<class T>  
BOOST_MOVE_FORCEINLINE T launder_cast(const volatile void* p)  
{  
   return (launder)(static_cast<T>(const_cast<void*>(p)));  
}  
```  
  
I don't think `boost::container::static_vector` is at fault here, as the helper above really is casting away the _cv_-qualifiers on its argument.  
  
It's a _safe_ cast, as long as this helper is only called—directly or indirectly—by a container or allocator that's managing its own pool of mutable raw storage. AFAICT, the purpose of the `const volatile void *` argument is for it to serve as a "universal pointer recipient" for all ordinary pointers, similar to `delete`.  And, like `delete`, it should ignore _cv_-qualifiers on the supplied argument.  
  
I don't know for certain whether this laundering approach is actually kosher; however, that's part of the [larger discussion](https://www.open-std.org/jtc1/sc22/wg21/docs/papers/2017/p0532r0.pdf) of how to do what `std::launder` attempts to do.  I'm not going there. 😄

---

## Comment 1

> Username: lynqagearchitect  
> Created at: 2025-08-07 02:28:25 UTC  
> Url: https://github.com/boostorg/move/issues/61#issuecomment-3162206630  

I forgot to include my tool version.  
  
```  
$ clang++ --version  
Apple clang version 17.0.0 (clang-1700.0.13.5)  
Target: arm64-apple-darwin24.6.0  
Thread model: posix  
InstalledDir: /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin  
```  
  
The exact Clang++ command line, after redacting the project filenames:  
  
```  
clang++ -o obj/my_source_file.o -O3 -march=native -mtune=native -std=gnu++23 -flto=auto -I/opt/homebrew/Cellar/boost/1.88.0/include/ -ggdb3 -ffunction-sections -fdata-sections -fno-common -ferror-limit=2 -I/opt/homebrew/Cellar/gperftools/2.16/include -I./inc -Wall -W -Wextra -Werror -Wshadow -Wcast-qual -Wsequence-point -Wstrict-aliasing -Wnon-virtual-dtor -Wodr -c -I/opt/homebrew/Cellar/googletest/1.17.0/include -DGTEST_HAS_PTHREAD=1 src/my_source_file.cc  
```
