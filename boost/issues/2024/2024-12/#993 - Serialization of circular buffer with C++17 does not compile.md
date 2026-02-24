# #993 - Serialization of circular buffer with C++17 does not compile [Closed]

> Username: nedsana  
> Created at: 2024-12-20 13:26:22 UTC  
> Updated at: 2024-12-23 14:52:41 UTC  
> Closed at: 2024-12-23 14:52:40 UTC  
> Url: https://github.com/boostorg/boost/issues/993  

Hi,   
  
I have troubles updating from boost 1.65 to boost 1.69. For this purpose I have created the following setup:   
- Ubuntu 18.04 - Bionic  
- GCC 11.4.0 with C++17  
- manually compile and install boost 1.69: (./bootstrap.sh --prefix=/usr/; ./b2 -j8; sudo ./b2 install; sudo ldconfig)  
  
  
The code below illustrates my problem. Basically I have:  
-- **multi-index container**, holding  
---- **shared pointers** to   
------- struct with member  
---------- **circular_buffer**  
  
The compilation fails with **boost 1.69 and C++17** (set in the CMakeLists.txt file below). The error is:  
  
```  
[ 50%] Building CXX object CMakeFiles/test.dir/test.cpp.o  
In file included from /usr/include/boost/circular_buffer/base.hpp:25,  
                 from /usr/include/boost/circular_buffer.hpp:55,  
                 from /home/corenet/Projects/test_multi_index_and_circular_buffer/test.cpp:15:  
/usr/include/boost/core/empty_value.hpp: In instantiation of 'struct boost::use_empty_value_base<boost::serialization::U>':  
/usr/include/boost/core/empty_value.hpp:46:46:   required by substitution of 'template<class Archive, template<class U> class SPT> void boost::serialization::load(Archive&, SPT<boost::serialization::U>&, unsigned int) [with Archive = boost::archive::xml_iarchive; SPT = <missing>]'  
/usr/include/boost/serialization/split_free.hpp:58:13:   required from 'static void boost::serialization::free_loader<Archive, T>::invoke(Archive&, T&, unsigned int) [with Archive = boost::archive::xml_iarchive; T = boost::circular_buffer<int>]'  
/usr/include/boost/serialization/split_free.hpp:74:18:   required from 'void boost::serialization::split_free(Archive&, T&, unsigned int) [with Archive = boost::archive::xml_iarchive; T = boost::circular_buffer<int>]'  
/home/corenet/Projects/test_multi_index_and_circular_buffer/test.cpp:57:19:   required from 'void boost::serialization::serialize(Archive&, boost::circular_buffer<T>&, unsigned int) [with Archive = boost::archive::xml_iarchive; T = int]'  
/usr/include/boost/serialization/serialization.hpp:126:14:   required from 'void boost::serialization::serialize_adl(Archive&, T&, unsigned int) [with Archive = boost::archive::xml_iarchive; T = boost::circular_buffer<int>]'  
/usr/include/boost/archive/detail/iserializer.hpp:187:40:   [ skipping 67 instantiation contexts, use -ftemplate-backtrace-limit=0 to disable ]  
/usr/include/boost/archive/basic_xml_iarchive.hpp:78:52:   required from 'void boost::archive::basic_xml_iarchive<Archive>::load_override(const boost::serialization::nvp<T>&) [with T = mru_list<std::shared_ptr<dummy> >; Archive = boost::archive::xml_iarchive]'  
/usr/include/boost/archive/xml_iarchive.hpp:95:51:   required from 'void boost::archive::xml_iarchive_impl<Archive>::load_override(T&) [with T = const boost::serialization::nvp<mru_list<std::shared_ptr<dummy> > >; Archive = boost::archive::xml_iarchive]'  
/usr/include/boost/archive/detail/interface_iarchive.hpp:68:36:   required from 'Archive& boost::archive::detail::interface_iarchive<Archive>::operator>>(T&) [with T = const boost::serialization::nvp<mru_list<std::shared_ptr<dummy> > >; Archive = boost::archive::xml_iarchive]'  
/usr/include/boost/archive/detail/interface_iarchive.hpp:75:32:   required from 'Archive& boost::archive::detail::interface_iarchive<Archive>::operator&(T&) [with T = const boost::serialization::nvp<mru_list<std::shared_ptr<dummy> > >; Archive = boost::archive::xml_iarchive]'  
/home/corenet/Projects/test_multi_index_and_circular_buffer/test.cpp:136:10:   required from 'void mru_list<Item>::load(const char*) [with Item = std::shared_ptr<dummy>]'  
/home/corenet/Projects/test_multi_index_and_circular_buffer/test.cpp:158:15:   required from here  
/usr/include/boost/core/empty_value.hpp:34:17: error: invalid use of incomplete type 'class boost::serialization::U'  
   34 |         value = __is_empty(T) && !__is_final(T)  
      |                 ^~~~~~~~~~~~~  
In file included from /usr/include/boost/serialization/shared_ptr.hpp:29,  
                 from /home/corenet/Projects/test_multi_index_and_circular_buffer/test.cpp:10:  
/usr/include/boost/serialization/shared_ptr_helper.hpp:45:16: note: forward declaration of 'class boost::serialization::U'  
   45 |     SPT< class U > &t,  
      |                ^  
In file included from /usr/include/boost/circular_buffer/base.hpp:25,  
                 from /usr/include/boost/circular_buffer.hpp:55,  
                 from /home/corenet/Projects/test_multi_index_and_circular_buffer/test.cpp:15:  
/usr/include/boost/core/empty_value.hpp:34:35: error: invalid use of incomplete type 'class boost::serialization::U'  
   34 |         value = __is_empty(T) && !__is_final(T)  
      |                                   ^~~~~~~~~~~~~  
In file included from /usr/include/boost/serialization/shared_ptr.hpp:29,  
                 from /home/corenet/Projects/test_multi_index_and_circular_buffer/test.cpp:10:  
/usr/include/boost/serialization/shared_ptr_helper.hpp:45:16: note: forward declaration of 'class boost::serialization::U'  
   45 |     SPT< class U > &t,  
      |                ^  
  
```  
  
I did the following checks, as well:  
- The compilation with **boost 1.69 and C++14** (set in the CMakeLists.txt file below) is OK.  
- The compilation with **_boost 1.65  and C++17_** (set in the CMakeLists.txt file below) is OK.  
- The compilation with **boost 1.80  and C++17** (set in the CMakeLists.txt file below)  is FAIL-ing with the error above.  
  
My guess is that the issue is related to the introduction of #include <boost/core/empty_value.hpp> and the inheriting of empty_value by circular_buffer. But I have no clue how to solve the issue.   
  
Could you please give me a hand?  
Thank you in advance!  
  
The code:  
  
CMakeLists.txt  
```  
cmake_minimum_required(VERSION 3.10)  
project(test)  
set(CMAKE_CXX_STANDARD 17)  
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wall")  
find_package (Boost 1.53 REQUIRED COMPONENTS serialization)  
add_executable(test test.cpp)  
target_link_libraries(test pthread ${Boost_LIBRARIES})  
```  
  
test.cpp  
```  
#include <boost/multi_index_container.hpp>  
#include <boost/multi_index/hashed_index.hpp>  
#include <boost/multi_index/sequenced_index.hpp>  
#include <boost/multi_index/ordered_index.hpp>  
#include <boost/archive/xml_iarchive.hpp>  
#include <boost/archive/xml_oarchive.hpp>  
#include <boost/serialization/nvp.hpp>  
#include <boost/serialization/serialization.hpp>  
#include <boost/serialization/version.hpp>  
#include <boost/serialization/shared_ptr.hpp>  
#include <boost/serialization/split_free.hpp>  
#include <fstream>  
  
#include <boost/circular_buffer.hpp>  
#include <boost/serialization/split_free.hpp>  
  
namespace boost  
{  
  namespace serialization  
  {  
  
    template <class Archive, class T>  
    void save(Archive& ar, const circular_buffer<T>& b, const unsigned int version)  
    {  
        typename circular_buffer<T>::size_type size = b.size();  
        typename circular_buffer<T>::capacity_type capacity = b.capacity();  
        ar & BOOST_SERIALIZATION_NVP(capacity);  
        ar & BOOST_SERIALIZATION_NVP(size);  
        while (size > 0) {  
            --size;  
            const T& elem = b[size];  
            ar & BOOST_SERIALIZATION_NVP(elem);  
        }  
    }  
  
    template <class Archive, class T>  
    void load(Archive& ar, circular_buffer<T>& b, const unsigned int version)  
    {  
        typename circular_buffer<T>::capacity_type capacity;  
        typename circular_buffer<T>::size_type size;  
        ar & BOOST_SERIALIZATION_NVP(capacity);  
        b.set_capacity(capacity);  
        ar & BOOST_SERIALIZATION_NVP(size);  
        b.clear();  
        T elem;  
        while (size > 0) {  
            --size;  
            ar & BOOST_SERIALIZATION_NVP(elem);  
            b.push_front(elem);  
        }  
    }  
  
    template<class Archive, class T>  
    inline void serialize(Archive & ar, circular_buffer<T>& b, const unsigned int version)  
    {  
        split_free(ar, b, version);  
    }  
  
  } //namespace serialization  
} //namespace boost  
  
struct dummy  
{  
  int32_t                     m0 {55};  
  double                      m1 {66.66};  
  std::string                 m2 {"BLABLA"};  
  boost::circular_buffer<int> m3 {10};  
  
  dummy() = default;  
  
  dummy(int32_t v0, double v1, std::string v2, std::vector<int> v3) : m0(v0), m1(v1), m2(v2)  
  {  
    for (auto&& v : v3)  
      m3.push_back(v);  
  }  
  
  bool operator == (const dummy &d)  
  {  
      bool r {true};  
      if (r) r = m0 == d.m0;  
      if (r) r = m1 == d.m1;  
      if (r) r = m2 == d.m2;  
      if (r) r = m3 == d.m3;  
      return r;   
  }   
  
  friend class boost::serialization::access;  
      
  template<class Archive>  
  void serialize(Archive& ar,const unsigned int)  
  {  
    ar & BOOST_SERIALIZATION_NVP(m0);  
    ar & BOOST_SERIALIZATION_NVP(m1);  
    ar & BOOST_SERIALIZATION_NVP(m2);  
  
   //============================================================  
    ar & BOOST_SERIALIZATION_NVP(m3); // THIS LINE CAUSES THE COMPILATION TO CRASH!!!  
   //============================================================  
  
  }  
};  
  
using boost::multi_index_container;  
using namespace boost::multi_index;  
  
template <typename Item>  
class mru_list  
{  
  typedef multi_index_container<  
    Item,  
    indexed_by<  
      sequenced<>,  
      hashed_unique<identity<Item>>  
    >  
  > item_list;  
  
public:  
  typedef Item                         item_type;  
  typedef typename item_list::iterator iterator;  
  
  mru_list(std::size_t n)   
  {  
    for (size_t i=0; i<n; i++)  
      il.push_front(std::make_shared<dummy>(i, double(i), std::to_string(i), std::vector<int>(5, i)));  
  }  
  
  void save(const char* file_name)const  
  {  
    std::ofstream ofs(file_name);  
    boost::archive::xml_oarchive oa(ofs);  
    oa & boost::serialization::make_nvp("mru",*this);  
  }  
  
  void load(const char* file_name)  
  {  
    std::ifstream ifs(file_name);  
    if(ifs){  
      boost::archive::xml_iarchive ia(ifs);  
      ia & boost::serialization::make_nvp("mru",*this);  
    }  
  }  
  
private:  
  item_list   il;  
  
  friend class boost::serialization::access;  
      
  template<class Archive>  
  void serialize(Archive& ar,const unsigned int)  
  {  
    ar & BOOST_SERIALIZATION_NVP(il);  
  }  
};  
  
int main()  
{  
  mru_list<std::shared_ptr<dummy>> to_save(3);  
  mru_list<std::shared_ptr<dummy>> to_load(0);  
  
  to_save.save("stored.txt");  
  to_load.load("stored.txt");  
  to_load.save("loaded.txt");  
}  
  
```

---

## Comment 1

> Username: nedsana  
> Created at: 2024-12-23 14:52:40 UTC  
> Url: https://github.com/boostorg/boost/issues/993#issuecomment-2559846518  

Reported earlier:  
- https://github.com/boostorg/serialization/pull/144  
- https://github.com/boostorg/serialization/pull/144/commits/7b6eecf6f1d42c887eaefb77c412c47b1777c576  
  
Closing.
