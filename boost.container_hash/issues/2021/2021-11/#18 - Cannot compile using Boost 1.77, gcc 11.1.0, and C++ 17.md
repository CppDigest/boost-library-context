# #18 - Cannot compile using Boost 1.77, gcc 11.1.0, and C++ 17 [Closed]

> Username: ghost  
> Created at: 2021-11-18 19:38:12 UTC  
> Updated at: 2022-02-05 00:37:11 UTC  
> Closed at: 2022-02-05 00:37:11 UTC  
> Url: https://github.com/boostorg/container_hash/issues/18  

I'm trying to upgrade a project that used to use Boost 1.63 and gcc 6.5 to Boost 1.77 and gcc 11.1.0  
I am able to compile using flags for c++ 11 and c++ 14 but not c++ 17  
  
This is the error I get from the container hash library and I'm not sure what's wrong here:  
```  
In file included from /home/dev/boost/include/boost/container_hash/hash.hpp:761,  
                 from /home/dev/boost/include/boost/functional/hash.hpp:6,  
                 from /home/dev/boost/include/boost/unordered/unordered_map.hpp:18,  
                 from /home/dev/boost/include/boost/unordered_map.hpp:17,  
                 from /home/dev/gcc_stapl/stapl/tools/boost/serialization/unordered_map.hpp:27,  
                 from /home/dev/gcc_stapl/stapl/tools/libstdc++/stl_define_types.h:250,  
                 from /home/dev/gcc_stapl/stapl/./tools/libstdc++/11.1.0/bits/stl_vector.h:1994,  
                 from /usr/include/c++/11/vector:67,  
                 from /usr/include/c++/11/functional:62,  
                 from /home/dev/boost/include/boost/container_hash/hash.hpp:20,  
                 from /home/dev/boost/include/boost/functional/hash.hpp:6,  
                 from /home/dev/gcc_stapl/stapl/stapl/utility/hash_fwd.hpp:41,  
                 from p_object_registry.cc:29:  
/home/dev/boost/include/boost/container_hash/extensions.hpp:300:40: error: expected template-name before ‘<’ token  
  300 |         : boost::hash_detail::hash_base<T>  
      |                                        ^  
/home/dev/boost/include/boost/container_hash/extensions.hpp:300:40: error: expected ‘{’ before ‘<’ token  
In file included from /home/dev/boost/include/boost/functional/hash.hpp:6,  
                 from /home/dev/gcc_stapl/stapl/stapl/utility/hash_fwd.hpp:41,  
                 from p_object_registry.cc:29:  
/home/dev/boost/include/boost/container_hash/hash.hpp: In instantiation of ‘void boost::hash_combine(std::size_t&, const T&) [with T = stapl::runtime::full_location; std::size_t = long unsigned int]’:  
/home/dev/gcc_stapl/stapl/stapl/runtime/context_id.hpp:159:24:   required from here  
/home/dev/boost/include/boost/container_hash/hash.hpp:415:24: error: ‘boost::hash<stapl::runtime::full_location> hasher’ has incomplete type  
  415 |         boost::hash<T> hasher;  
      |                        ^~~~~~  
/home/dev/boost/include/boost/container_hash/hash.hpp: In instantiation of ‘void boost::hash_combine(std::size_t&, const T&) [with T = stapl::runtime::object_virtual_address::address_type; std::size_t = long unsigned int]’:  
/home/dev/gcc_stapl/stapl/stapl/runtime/rmi_handle_fwd.hpp:158:24:   required from here  
/home/dev/boost/include/boost/container_hash/hash.hpp:415:24: error: ‘boost::hash<stapl::runtime::object_virtual_address::address_type> hasher’ has incomplete type  
```

---

## Comment 1

> Username: ecatmur  
> Created at: 2021-12-08 11:11:24 UTC  
> Url: https://github.com/boostorg/container_hash/issues/18#issuecomment-988718812  

from /home/dev/gcc_stapl/stapl/./tools/libstdc++/11.1.0/bits/stl_vector.h:1994,  
                 from /usr/include/c++/11/vector:67,  
  
It looks like you're faking a Standard Library header. This is undefined behavior, so Boost.ContainerHash failing to compile is the least of your worries. Talk to whoever wrote `/home/dev/gcc_stapl/stapl/./tools/libstdc++/11.1.0/bits/stl_vector.h` and get them to fix it; this is not the fault of Boost.

---

## Comment 2

> Username: jzmaddock  
> Created at: 2021-12-08 11:19:04 UTC  
> Url: https://github.com/boostorg/container_hash/issues/18#issuecomment-988724463  

+1 on @ecatmur comments, but it loks like a circular include issue - ie your homebrew std lib is #including boost headers which are then trying to #include the std lib.
