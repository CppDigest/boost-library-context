# #62 - Document input_iterator incompatibility with pre-C++20 interfaces (was: Compilation failure on 1.83.0 release candidate (regression from 1.82.0)) [Open]

> Username: mclow  
> Created at: 2023-08-07 03:48:29 UTC  
> Updated at: 2023-09-19 00:01:23 UTC  
> Url: https://github.com/boostorg/stl_interfaces/issues/62  

Reported on the mailing list:  
  
```  
#include "boost/stl_interfaces/iterator_interface.hpp"  
#include "boost/range/iterator_range.hpp"  
  
  
struct basic_input_iter  
   : boost::stl_interfaces::iterator_interface<  
#if !BOOST_STL_INTERFACES_USE_DEDUCED_THIS  
         basic_input_iter,  
#endif  
         std::input_iterator_tag, int>  
{  
   basic_input_iter() : it_(nullptr) {}  
   basic_input_iter(int * it) : it_(it) {}  
  
   int & operator*() const noexcept { return *it_; }  
   basic_input_iter & operator++() noexcept  
   {  
       ++it_;  
       return *this;  
   }  
   friend bool operator==(basic_input_iter lhs, basic_input_iter rhs) noexcept  
   {  
       return lhs.it_ == rhs.it_;  
   }  
  
   using base_type = boost::stl_interfaces::iterator_interface<  
#if !BOOST_STL_INTERFACES_USE_DEDUCED_THIS  
       basic_input_iter,  
#endif  
       std::input_iterator_tag, int>;  
   using base_type::operator++;  
  
private:  
   int * it_;  
};  
  
  
auto make_range() {  
 static int si;  
 return boost::make_iterator_range(  
     basic_input_iter(&si),  
     basic_input_iter(&si + 1));  
}  
```  
  
This fails to compile with the following errors:  
  
g++ -Wall -Wstrict-aliasing=0 -Wno-unused-local-typedefs -Werror=return-type   -static-libgcc -static-libasan -static-libstdc++ -static-libubsan -std=gnu++20  -O3 -flto -pthread -Iintermediate/linux64/obj/linux64__test__iter_test  Iintermediate/linux64/libs/boost/install/include -DELDWOOD_EXTENSION_BOOST -c -o intermediate/linux64/obj/linux64__test__iter_test/eldwood/test/iter.cpp.o intermediate/linux64/obj/linux64__test__iter_test/eldwood/test/iter.cpp  
In file included from intermediate/linux64/libs/boost/install/include/boost/iterator/iterator_facade.hpp:13,  
                from intermediate/linux64/libs/boost/install/include/boost/range/iterator_range_core.hpp:27,  
                from intermediate/linux64/libs/boost/install/include/boost/range/iterator_range.hpp:13,  
                from intermediate/linux64/obj/linux64__test__iter_test/eldwood/test/iter.cpp:2:  
intermediate/linux64/libs/boost/install/include/boost/iterator/iterator_categories.hpp: In instantiation of ‘struct boost::iterators::iterator_traversal<basic_input_iter>’:  
intermediate/linux64/libs/boost/install/include/boost/range/iterator_range_core.hpp:158:5:   required from ‘struct boost::iterator_range_detail::pure_iterator_traversal<basic_input_iter>’  
intermediate/linux64/libs/boost/install/include/boost/range/iterator_range_core.hpp:434:15:   required from ‘class boost::iterator_range<basic_input_iter>’  
intermediate/linux64/obj/linux64__test__iter_test/eldwood/test/iter.cpp:40:36:   required from here  
intermediate/linux64/libs/boost/install/include/boost/iterator/iterator_categories.hpp:118:8: error: no type named ‘iterator_category’ in ‘struct std::iterator_traits<basic_input_iter>’  
 118 | struct iterator_traversal  
     |        ^~~~~~~~~~~~~~~~~~  
intermediate/linux64/libs/boost/install/include/boost/range/iterator_range_core.hpp: In instantiation of ‘struct boost::iterator_range_detail::pure_iterator_traversal<basic_input_iter>’:  
intermediate/linux64/libs/boost/install/include/boost/range/iterator_range_core.hpp:434:15:   required from ‘class boost::iterator_range<basic_input_iter>’  
intermediate/linux64/obj/linux64__test__iter_test/eldwood/test/iter.cpp:40:36:   required from here  
intermediate/linux64/libs/boost/install/include/boost/range/iterator_range_core.hpp:158:5: error: no type named ‘type’ in ‘struct boost::iterators::iterator_traversal<basic_input_iter>’  
 158 |     traversal_t;  
     |     ^~~~~~~~~~~  
In file included from intermediate/linux64/libs/boost/install/include/boost/utility/enable_if.hpp:15,  
                from intermediate/linux64/libs/boost/install/include/boost/range/has_range_iterator.hpp:21,  
                from intermediate/linux64/libs/boost/install/include/boost/range/difference_type.hpp:21,  
                from intermediate/linux64/libs/boost/install/include/boost/range/size_type.hpp:19,  
                from intermediate/linux64/libs/boost/install/include/boost/range/size.hpp:21,  
                from intermediate/linux64/libs/boost/install/include/boost/range/functions.hpp:20,  
                from intermediate/linux64/libs/boost/install/include/boost/range/iterator_range_core.hpp:38:  
intermediate/linux64/libs/boost/install/include/boost/core/enable_if.hpp: In instantiation of ‘struct boost::enable_if<boost::iterator_range<basic_input_iter>::is_compatible_range<const basic_input_iter>, void>’:  
intermediate/linux64/libs/boost/install/include/boost/range/iterator_range_core.hpp:489:13:   required by substitution of ‘template<class SinglePassRange> boost::iterator_range<basic_input_iter>::iterator_range(const SinglePassRange&, typename boost::enable_if<is_compatible_range<const SinglePassRange>, void>::type*) [with SinglePassRange = basic_input_iter]’  
intermediate/linux64/libs/boost/install/include/boost/range/iterator_range_core.hpp:758:20:   required from ‘boost::iterator_range<IteratorT> boost::make_iterator_range(IteratorT, IteratorT) [with IteratorT = basic_input_iter]’  
intermediate/linux64/obj/linux64__test__iter_test/eldwood/test/iter.cpp:40:36:   required from here  
intermediate/linux64/libs/boost/install/include/boost/core/enable_if.hpp:41:10: error: ‘value’ is not a member of ‘boost::iterator_range<basic_input_iter>::is_compatible_range<const basic_input_iter>’  
  41 |   struct enable_if : public enable_if_c<Cond::value, T> {};  
     |          ^~~~~~~~~  
intermediate/linux64/libs/boost/install/include/boost/core/enable_if.hpp: In instantiation of ‘struct boost::enable_if<boost::iterator_range<basic_input_iter>::is_compatible_range<basic_input_iter>, void>’:  
intermediate/linux64/libs/boost/install/include/boost/range/iterator_range_core.hpp:500:13:   required by substitution of ‘template<class SinglePassRange> boost::iterator_range<basic_input_iter>::iterator_range(SinglePassRange&, typename boost::enable_if<is_compatible_range<Source>, void>::type*) [with SinglePassRange = basic_input_iter]’  
intermediate/linux64/libs/boost/install/include/boost/range/iterator_range_core.hpp:758:20:   required from ‘boost::iterator_range<IteratorT> boost::make_iterator_range(IteratorT, IteratorT) [with IteratorT = basic_input_iter]’  
intermediate/linux64/obj/linux64__test__iter_test/eldwood/test/iter.cpp:40:36:   required from here  
intermediate/linux64/libs/boost/install/include/boost/core/enable_if.hpp:41:10: error: ‘value’ is not a member of ‘boost::iterator_range<basic_input_iter>::is_compatible_range<basic_input_iter>’  
  
Build failed.

---

## Comment 1

> Username: mclow  
> Created at: 2023-08-07 17:13:09 UTC  
> Url: https://github.com/boostorg/stl_interfaces/issues/62#issuecomment-1668279536  

Joaquín M López Muñoz wrote:  
  
Looks like the iterator_concept -> iterator_category mapping used here is [incomplete](  
https://github.com/boostorg/stl_interfaces/blob/develop/include/boost/stl_interfaces/iterator_interface.hpp#L650-L676)  
  
The mapping is copied verbatim from zip_transform_view as spelled out at:  
  
https://eel.is/c++draft/range.zip.transform.iterator#1  
  
I guess this is ok for zip_transform_view, but the description of the mapping explicitly says  
  
"The member typedef-name iterator​::​iterator_category is defined if and only if Base models forward_range."  
  
Note the "if and only if", implying that if Base only models input_range then iterator​::​iterator_category  
is not defined.  
  
FWIW, the attached patch makes the snippet compile. It simply adds and input_iterator_tag -> input_iterator_tag  
case to the mapping algorithm.  
  
[iterator_interface.hpp.patch](https://github.com/boostorg/stl_interfaces/files/12283342/iterator_interface.hpp.patch)

---

## Comment 2

> Username: mclow  
> Created at: 2023-08-07 18:46:51 UTC  
> Url: https://github.com/boostorg/stl_interfaces/issues/62#issuecomment-1668410706  

Looks like it came from this commit: https://github.com/boostorg/stl_interfaces/commit/21e03e70130c65bb5335a15ea5d49fe0b08efbea

---

## Comment 3

> Username: mclow  
> Created at: 2023-08-08 19:49:52 UTC  
> Url: https://github.com/boostorg/stl_interfaces/issues/62#issuecomment-1670216548  

Zach says: Hey, Marshall.   I'm on vacation and didn't even have my phone on yesterday.  It looks like "Works as designed"  to me.  The design changed so that iterators are not compatible with pre 20 interfaces, based on LWG and LEWG feedback.  The Boost library reflects that.  I can take a closer look this weekend to verify.
