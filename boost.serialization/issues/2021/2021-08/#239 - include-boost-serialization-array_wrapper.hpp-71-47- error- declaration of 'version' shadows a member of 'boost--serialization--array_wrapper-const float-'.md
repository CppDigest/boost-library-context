# #239 - include/boost/serialization/array_wrapper.hpp:71:47: error: declaration of 'version' shadows a member of 'boost::serialization::array_wrapper<const float>' [-Werror=shadow] [Closed]

> Username: stixpjr  
> Created at: 2021-08-14 14:22:05 UTC  
> Updated at: 2023-08-25 03:03:00 UTC  
> Closed at: 2023-08-24 18:19:45 UTC  
> Url: https://github.com/boostorg/serialization/issues/239  

NetBSD 9.99.87, x86_64 with gcc version 10.3.0 (nb1 20210411).  
While attempting to build https://github.com/USCiLab/cereal v1.3.0.  
  
Easily fixed by renaming the function variables "version" to something else (eg. "vers") in boost/serialization/array_wrapper.hpp  
  
Full error:  
In file included from /usr/tmp/pkgwrk/wip/cereal/work/.buildlink/include/boost/archive/basic_binary_oprimitive.hpp:47,                                                                                                                                                                  
                 from /usr/tmp/pkgwrk/wip/cereal/work/.buildlink/include/boost/archive/binary_oarchive_impl.hpp:21,                                                                                                                                                                     
                 from /usr/tmp/pkgwrk/wip/cereal/work/.buildlink/include/boost/archive/binary_oarchive.hpp:21,                                                                                                                                                                          
                 from /usr/tmp/pkgwrk/wip/cereal/work/cereal-1.3.0/sandbox/performance.cpp:43:                                                                                                                                                                                          
/usr/tmp/pkgwrk/wip/cereal/work/.buildlink/include/boost/serialization/array_wrapper.hpp: In instantiation of 'void boost::serialization::array_wrapper<T>::serialize(Archive&, unsigned int) [with Archive = boost::archive::binary_oarchive; T = const double]':                      
/usr/tmp/pkgwrk/wip/cereal/work/.buildlink/include/boost/serialization/access.hpp:116:20:   required from 'static void boost::serialization::access::serialize(Archive&, T&, unsigned int) [with Archive = boost::archive::binary_oarchive; T = boost::serialization::array_wrapper<co  
nst double>]'                                                                                                                                                                                                                                                                           
/usr/tmp/pkgwrk/wip/cereal/work/.buildlink/include/boost/serialization/serialization.hpp:59:22:   required from 'void boost::serialization::serialize(Archive&, T&, unsigned int) [with Archive = boost::archive::binary_oarchive; T = boost::serialization::array_wrapper<const doubl  
e>]'                                                                                                                                                                                                                                                                                    
/usr/tmp/pkgwrk/wip/cereal/work/.buildlink/include/boost/serialization/serialization.hpp:109:14:   required from 'void boost::serialization::serialize_adl(Archive&, T&, unsigned int) [with Archive = boost::archive::binary_oarchive; T = boost::serialization::array_wrapper<const   
double>]'                                                                                                                                                                                                                                                                               
/usr/tmp/pkgwrk/wip/cereal/work/.buildlink/include/boost/archive/detail/oserializer.hpp:246:48:   required from 'static void boost::archive::detail::save_non_pointer_type<Archive>::save_only::invoke(Archive&, const T&) [with T = boost::serialization::array_wrapper<const double>  
; Archive = boost::archive::binary_oarchive]'                                                                                                                                                                                                                                           
/usr/tmp/pkgwrk/wip/cereal/work/.buildlink/include/boost/archive/detail/oserializer.hpp:315:22:   required from 'static void boost::archive::detail::save_non_pointer_type<Archive>::invoke(Archive&, const T&) [with T = boost::serialization::array_wrapper<const double>; Archive =  
 boost::archive::binary_oarchive]'                                                                                                                                                                                                                                                      
/usr/tmp/pkgwrk/wip/cereal/work/.buildlink/include/boost/archive/detail/oserializer.hpp:539:18:   [ skipping 20 instantiation contexts, use -ftemplate-backtrace-limit=0 to disable ]                                                                                                   
/usr/tmp/pkgwrk/wip/cereal/work/.buildlink/include/boost/archive/detail/interface_oarchive.hpp:70:36:   required from 'Archive& boost::archive::detail::interface_oarchive<Archive>::operator<<(const T&) [with T = std::vector<double>; Archive = boost::archive::binary_oarchive]'    
/usr/tmp/pkgwrk/wip/cereal/work/.buildlink/include/boost/archive/detail/interface_oarchive.hpp:77:32:   required from 'Archive& boost::archive::detail::interface_oarchive<Archive>::operator&(const T&) [with T = std::vector<double>; Archive = boost::archive::binary_oarchive]'     
/usr/tmp/pkgwrk/wip/cereal/work/cereal-1.3.0/sandbox/performance.cpp:119:9:   required from 'static void boostBinary::save(std::ostringstream&, const T&) [with T = std::vector<double>; std::ostringstream = std::__cxx11::basic_ostringstream<char>]'                                 
/usr/tmp/pkgwrk/wip/cereal/work/cereal-1.3.0/sandbox/performance.cpp:180:45:   required from 'void test(const string&, const DataTCereal&, const DataTBoost&, size_t, bool) [with SerializationT = binary; DataTCereal = std::vector<double>; DataTBoost = std::vector<double>; std::s  
tring = std::__cxx11::basic_string<char>; size_t = long unsigned int]'                                                                                                                                                                                                                  
/usr/tmp/pkgwrk/wip/cereal/work/cereal-1.3.0/sandbox/performance.cpp:237:44:   required from 'void test(const string&, const DataT&, size_t, bool) [with SerializationT = binary; DataT = std::vector<double>; std::string = std::__cxx11::basic_string<char>; size_t = long unsigned   
int]'                                                                                                                                                                                                                                                                                   
/usr/tmp/pkgwrk/wip/cereal/work/cereal-1.3.0/sandbox/performance.cpp:360:36:   required from here                                                                                                                                                                                       
/usr/tmp/pkgwrk/wip/cereal/work/.buildlink/include/boost/serialization/array_wrapper.hpp:85:52: error: declaration of 'version' shadows a member of 'boost::serialization::array_wrapper<const double>' [-Werror=shadow]                                                                
   85 |     void serialize(Archive &ar, const unsigned int version)                                                                                                                                                                                                                     
      |                                 ~~~~~~~~~~~~~~~~~~~^~~~~~~

---

## Comment 1

> Username: 0-wiz-0  
> Created at: 2023-06-07 14:50:53 UTC  
> Url: https://github.com/boostorg/serialization/issues/239#issuecomment-1580987215  

This looks simple enough - can this please be merged?

---

## Comment 2

> Username: robatra-jumptrading  
> Created at: 2023-06-21 19:17:14 UTC  
> Url: https://github.com/boostorg/serialization/issues/239#issuecomment-1601517139  

Experiencing this issue as well

---

## Comment 3

> Username: robertramey  
> Created at: 2023-08-24 18:19:45 UTC  
> Url: https://github.com/boostorg/serialization/issues/239#issuecomment-1692198768  

Apparently this issue occurs when building the serialization library "cereal" (very clever name!).  Not that this is not the Boost Serialization library so I don't think I be of help here.

---

## Comment 4

> Username: stixpjr  
> Created at: 2023-08-25 03:03:00 UTC  
> Url: https://github.com/boostorg/serialization/issues/239#issuecomment-1692688532  

I'm confused: a variable in `boost/serialization/array_wrapper.hpp` shadows a member of `boost::serialization::array_wrapper` and you say this isn't the Boost Serialization library?  
  
I've trimmed this down to a simple example which fails when built with `-Werror=shadow`  
  
```  
#include <iostream>  
  
#include "boost/archive/text_oarchive.hpp"  
  
int main() {  
        float f[5];  
        boost::archive::text_oarchive oa(std::cout);  
        oa & f;  
}  
```  
  
Fails with:  
  
```  
g++ -O2 -Werror=shadow -I/usr/pkg/include -L/usr/pkg/lib -lboost_serialization -Wl,-rpath,/usr/pkg/lib -o z z.cc  
In file included from /usr/pkg/include/boost/archive/detail/oserializer.hpp:63,  
                 from /usr/pkg/include/boost/archive/detail/interface_oarchive.hpp:23,  
                 from /usr/pkg/include/boost/archive/detail/common_oarchive.hpp:22,  
                 from /usr/pkg/include/boost/archive/basic_text_oarchive.hpp:29,  
                 from /usr/pkg/include/boost/archive/text_oarchive.hpp:31,  
                 from z.cc:4:  
/usr/pkg/include/boost/serialization/array_wrapper.hpp: In instantiation of 'void boost::serialization::array_wrapper<T>::serialize(Archive&, unsigned int) [with Archive = boost::archive::text_oarchive; T = const float]':  
/usr/pkg/include/boost/serialization/access.hpp:116:20:   required from 'static void boost::serialization::access::serialize(Archive&, T&, unsigned int) [with Archive = boost::archive::text_oarchive; T = boost::serialization::array_wrapper<const float>]'  
/usr/pkg/include/boost/serialization/serialization.hpp:59:22:   required from 'void boost::serialization::serialize(Archive&, T&, unsigned int) [with Archive = boost::archive::text_oarchive; T = boost::serialization::array_wrapper<const float>]'  
/usr/pkg/include/boost/serialization/serialization.hpp:109:14:   required from 'void boost::serialization::serialize_adl(Archive&, T&, unsigned int) [with Archive = boost::archive::text_oarchive; T = boost::serialization::array_wrapper<const float>]'  
/usr/pkg/include/boost/archive/detail/oserializer.hpp:248:48:   required from 'static void boost::archive::detail::save_non_pointer_type<Archive>::save_only::invoke(Archive&, const T&) [with T = boost::serialization::array_wrapper<const float>; Archive = boost::archive::text_oarchive]'  
/usr/pkg/include/boost/archive/detail/oserializer.hpp:317:22:   required from 'static void boost::archive::detail::save_non_pointer_type<Archive>::invoke(Archive&, const T&) [with T = boost::serialization::array_wrapper<const float>; Archive = boost::archive::text_oarchive]'  
/usr/pkg/include/boost/archive/detail/oserializer.hpp:541:18:   [ skipping 5 instantiation contexts, use -ftemplate-backtrace-limit=0 to disable ]  
/usr/pkg/include/boost/archive/detail/oserializer.hpp:541:18:   required from 'void boost::archive::save(Archive&, T&) [with Archive = boost::archive::text_oarchive; T = const float [5]]'  
/usr/pkg/include/boost/archive/detail/common_oarchive.hpp:71:22:   required from 'void boost::archive::detail::common_oarchive<Archive>::save_override(T&) [with T = const float [5]; Archive = boost::archive::text_oarchive]'  
/usr/pkg/include/boost/archive/basic_text_oarchive.hpp:83:52:   required from 'void boost::archive::basic_text_oarchive<Archive>::save_override(T&) [with T = const float [5]; Archive = boost::archive::text_oarchive]'  
/usr/pkg/include/boost/archive/detail/interface_oarchive.hpp:70:36:   required from 'Archive& boost::archive::detail::interface_oarchive<Archive>::operator<<(const T&) [with T = float [5]; Archive = boost::archive::text_oarchive]'  
/usr/pkg/include/boost/archive/detail/interface_oarchive.hpp:77:32:   required from 'Archive& boost::archive::detail::interface_oarchive<Archive>::operator&(const T&) [with T = float [5]; Archive = boost::archive::text_oarchive]'  
z.cc:9:7:   required from here  
/usr/pkg/include/boost/serialization/array_wrapper.hpp:85:52: warning: declaration of 'version' shadows a member of 'boost::serialization::array_wrapper<const float>' [-Wshadow]  
   85 |     void serialize(Archive &ar, const unsigned int version)  
      |                                 ~~~~~~~~~~~~~~~~~~~^~~~~~~  
In file included from /usr/pkg/include/boost/serialization/type_info_implementation.hpp:27,  
                 from /usr/pkg/include/boost/serialization/tracking.hpp:32,  
                 from /usr/pkg/include/boost/archive/detail/oserializer.hpp:58,  
                 from /usr/pkg/include/boost/archive/detail/interface_oarchive.hpp:23,  
                 from /usr/pkg/include/boost/archive/detail/common_oarchive.hpp:22,  
                 from /usr/pkg/include/boost/archive/basic_text_oarchive.hpp:29,  
                 from /usr/pkg/include/boost/archive/text_oarchive.hpp:31,  
                 from z.cc:4:  
/usr/pkg/include/boost/serialization/traits.hpp:57:41: note: shadowed declaration is here  
   57 |     typedef typename mpl::int_<Version> version;  
      |                                         ^~~~~~~  
```
