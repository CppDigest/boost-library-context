# #195 - namespace conflict on constructing objects in segment manager with named proxy in combination with boost geometry [Closed]

> Username: addy90  
> Created at: 2023-03-15 16:01:50 UTC  
> Updated at: 2023-09-20 15:34:42 UTC  
> Closed at: 2023-04-20 18:30:22 UTC  
> Url: https://github.com/boostorg/interprocess/issues/195  

Dear Boost colleagues,  
  
I am encountering a quite annoying compilation error that arises in combination with boost::geometry.  
  
Please see this code:  
```  
#include <boost/geometry/geometry.hpp>  
#include <boost/geometry/geometries/geometries.hpp>  
  
#include <boost/interprocess/managed_shared_memory.hpp>  
#include <boost/interprocess/allocators/cached_adaptive_pool.hpp>  
#include <boost/interprocess/containers/vector.hpp>  
  
int main(int argc, char *argv[]) {  
    struct shm_remove {  
        shm_remove() { boost::interprocess::shared_memory_object::remove("memory"); }  
  
        ~shm_remove() { boost::interprocess::shared_memory_object::remove("memory"); }  
    } remover;  
  
    using point_type = boost::geometry::model::point<double, 2, boost::geometry::cs::geographic<boost::geometry::degree>>;  
  
    using segment_manager_type = boost::interprocess::managed_shared_memory::segment_manager;  
    using point_allocator_type = boost::interprocess::cached_adaptive_pool<point_type, segment_manager_type>;  
    using points_type = boost::interprocess::vector<point_type, point_allocator_type>;  
  
    auto *memory = new boost::interprocess::managed_shared_memory{boost::interprocess::open_or_create, "memory", 1024};  
    auto *allocator = new point_allocator_type{memory->get_segment_manager()};  
    auto *data = memory->find_or_construct<points_type>("identifier")(*allocator);  
  
    delete allocator;  
    delete memory;  
}  
  
```  
  
It is compiled under Ubuntu 20.04 LTS with GCC 9.4.0 with the following command, when boost 1.81.0 exists under boost-build/include directory: \  
```g++ -pthread -isystem boost-build/include -o proxy proxy.cpp -lpthread -lrt```  
or without linking: \  
```g++ -isystem boost-build/include -o proxy.cpp.o -c proxy.cpp```  
 By the way, CLang10 also gives a similar error.  
  
The following error is given:  
```  
In file included from boost-build/include/boost/geometry/core/ring_type.hpp:27,  
                 from boost-build/include/boost/geometry/core/closure.hpp:23,  
                 from boost-build/include/boost/geometry/geometry.hpp:31,  
                 from proxy.cpp:1:  
boost-build/include/boost/geometry/core/point_type.hpp: In instantiation of ‘struct boost::geometry::traits::point_type<boost::container::dtl::tuple<boost::interprocess::cached_adaptive_pool<boost::geometry::model::point<double, 2, boost::geometry::cs::geographic<boost::geometry::degree> >, boost::interprocess::segment_manager<char, boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family, boost::interprocess::offset_ptr<void, long int, long unsigned int, 0>, 0>, boost::interprocess::iset_index>, 64, 2, 5>&> >’:  
boost-build/include/boost/geometry/core/point_type.hpp:71:17:   required from ‘struct boost::geometry::core_dispatch::point_type<void, boost::container::dtl::tuple<boost::interprocess::cached_adaptive_pool<boost::geometry::model::point<double, 2, boost::geometry::cs::geographic<boost::geometry::degree> >, boost::interprocess::segment_manager<char, boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family, boost::interprocess::offset_ptr<void, long int, long unsigned int, 0>, 0>, boost::interprocess::iset_index>, 64, 2, 5>&> >’  
boost-build/include/boost/geometry/core/coordinate_type.hpp:59:62:   required from ‘struct boost::geometry::core_dispatch::coordinate_type<void, boost::container::dtl::tuple<boost::interprocess::cached_adaptive_pool<boost::geometry::model::point<double, 2, boost::geometry::cs::geographic<boost::geometry::degree> >, boost::interprocess::segment_manager<char, boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family, boost::interprocess::offset_ptr<void, long int, long unsigned int, 0>, 0>, boost::interprocess::iset_index>, 64, 2, 5>&> >’  
boost-build/include/boost/geometry/core/coordinate_type.hpp:93:17:   required from ‘struct boost::geometry::coordinate_type<boost::container::dtl::tuple<boost::interprocess::cached_adaptive_pool<boost::geometry::model::point<double, 2, boost::geometry::cs::geographic<boost::geometry::degree> >, boost::interprocess::segment_manager<char, boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family, boost::interprocess::offset_ptr<void, long int, long unsigned int, 0>, 0>, boost::interprocess::iset_index>, 64, 2, 5>&> >’  
boost-build/include/boost/geometry/core/access.hpp:268:59:   required by substitution of ‘template<long unsigned int Dimension, class Geometry> constexpr typename boost::geometry::coordinate_type<Geometry2>::type boost::geometry::get(const Geometry&, boost::geometry::detail::signature_getset_dimension*) [with long unsigned int Dimension = 0; Geometry = boost::container::dtl::tuple<boost::interprocess::cached_adaptive_pool<boost::geometry::model::point<double, 2, boost::geometry::cs::geographic<boost::geometry::degree> >, boost::interprocess::segment_manager<char, boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family, boost::interprocess::offset_ptr<void, long int, long unsigned int, 0>, 0>, boost::interprocess::iset_index>, 64, 2, 5>&>]’  
boost-build/include/boost/interprocess/detail/named_proxy.hpp:85:84:   required from ‘void boost::interprocess::ipcdetail::CtorArgN<T, is_iterator, Args>::construct(void*, boost::interprocess::ipcdetail::false_, const boost::container::dtl::index_tuple<IdxPack ...>&) [with long unsigned int ...IdxPack = {0}; T = boost::container::vector<boost::geometry::model::point<double, 2, boost::geometry::cs::geographic<boost::geometry::degree> >, boost::interprocess::cached_adaptive_pool<boost::geometry::model::point<double, 2, boost::geometry::cs::geographic<boost::geometry::degree> >, boost::interprocess::segment_manager<char, boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family>, boost::interprocess::iset_index> > >; bool is_iterator = false; Args = {boost::interprocess::cached_adaptive_pool<boost::geometry::model::point<double, 2, boost::geometry::cs::geographic<boost::geometry::degree> >, boost::interprocess::segment_manager<char, boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family, boost::interprocess::offset_ptr<void, long int, long unsigned int, 0>, 0>, boost::interprocess::iset_index>, 64, 2, 5>&}; boost::interprocess::ipcdetail::false_ = boost::interprocess::ipcdetail::bool_<false>]’  
boost-build/include/boost/interprocess/detail/named_proxy.hpp:73:10:   required from ‘void boost::interprocess::ipcdetail::CtorArgN<T, is_iterator, Args>::construct_n(void*, std::size_t, std::size_t&) [with T = boost::container::vector<boost::geometry::model::point<double, 2, boost::geometry::cs::geographic<boost::geometry::degree> >, boost::interprocess::cached_adaptive_pool<boost::geometry::model::point<double, 2, boost::geometry::cs::geographic<boost::geometry::degree> >, boost::interprocess::segment_manager<char, boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family>, boost::interprocess::iset_index> > >; bool is_iterator = false; Args = {boost::interprocess::cached_adaptive_pool<boost::geometry::model::point<double, 2, boost::geometry::cs::geographic<boost::geometry::degree> >, boost::interprocess::segment_manager<char, boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family, boost::interprocess::offset_ptr<void, long int, long unsigned int, 0>, 0>, boost::interprocess::iset_index>, 64, 2, 5>&}; std::size_t = long unsigned int]’  
boost-build/include/boost/interprocess/detail/named_proxy.hpp:67:17:   required from here  
boost-build/include/boost/geometry/core/point_type.hpp:51:5: error: static assertion failed: Not implemented for this Geometry type.  
   51 |     BOOST_GEOMETRY_STATIC_ASSERT_FALSE(  
      |     ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
In file included from boost-build/include/boost/geometry/core/coordinate_dimension.hpp:24,  
                 from boost-build/include/boost/geometry/geometry.hpp:32,  
                 from proxy.cpp:1:  
boost-build/include/boost/geometry/core/point_type.hpp: In instantiation of ‘struct boost::geometry::core_dispatch::point_type<void, boost::container::dtl::tuple<boost::interprocess::cached_adaptive_pool<boost::geometry::model::point<double, 2, boost::geometry::cs::geographic<boost::geometry::degree> >, boost::interprocess::segment_manager<char, boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family, boost::interprocess::offset_ptr<void, long int, long unsigned int, 0>, 0>, boost::interprocess::iset_index>, 64, 2, 5>&> >’:  
boost-build/include/boost/geometry/core/coordinate_type.hpp:59:62:   required from ‘struct boost::geometry::core_dispatch::coordinate_type<void, boost::container::dtl::tuple<boost::interprocess::cached_adaptive_pool<boost::geometry::model::point<double, 2, boost::geometry::cs::geographic<boost::geometry::degree> >, boost::interprocess::segment_manager<char, boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family, boost::interprocess::offset_ptr<void, long int, long unsigned int, 0>, 0>, boost::interprocess::iset_index>, 64, 2, 5>&> >’  
boost-build/include/boost/geometry/core/coordinate_type.hpp:93:17:   required from ‘struct boost::geometry::coordinate_type<boost::container::dtl::tuple<boost::interprocess::cached_adaptive_pool<boost::geometry::model::point<double, 2, boost::geometry::cs::geographic<boost::geometry::degree> >, boost::interprocess::segment_manager<char, boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family, boost::interprocess::offset_ptr<void, long int, long unsigned int, 0>, 0>, boost::interprocess::iset_index>, 64, 2, 5>&> >’  
boost-build/include/boost/geometry/core/access.hpp:268:59:   required by substitution of ‘template<long unsigned int Dimension, class Geometry> constexpr typename boost::geometry::coordinate_type<Geometry2>::type boost::geometry::get(const Geometry&, boost::geometry::detail::signature_getset_dimension*) [with long unsigned int Dimension = 0; Geometry = boost::container::dtl::tuple<boost::interprocess::cached_adaptive_pool<boost::geometry::model::point<double, 2, boost::geometry::cs::geographic<boost::geometry::degree> >, boost::interprocess::segment_manager<char, boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family, boost::interprocess::offset_ptr<void, long int, long unsigned int, 0>, 0>, boost::interprocess::iset_index>, 64, 2, 5>&>]’  
boost-build/include/boost/interprocess/detail/named_proxy.hpp:85:84:   required from ‘void boost::interprocess::ipcdetail::CtorArgN<T, is_iterator, Args>::construct(void*, boost::interprocess::ipcdetail::false_, const boost::container::dtl::index_tuple<IdxPack ...>&) [with long unsigned int ...IdxPack = {0}; T = boost::container::vector<boost::geometry::model::point<double, 2, boost::geometry::cs::geographic<boost::geometry::degree> >, boost::interprocess::cached_adaptive_pool<boost::geometry::model::point<double, 2, boost::geometry::cs::geographic<boost::geometry::degree> >, boost::interprocess::segment_manager<char, boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family>, boost::interprocess::iset_index> > >; bool is_iterator = false; Args = {boost::interprocess::cached_adaptive_pool<boost::geometry::model::point<double, 2, boost::geometry::cs::geographic<boost::geometry::degree> >, boost::interprocess::segment_manager<char, boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family, boost::interprocess::offset_ptr<void, long int, long unsigned int, 0>, 0>, boost::interprocess::iset_index>, 64, 2, 5>&}; boost::interprocess::ipcdetail::false_ = boost::interprocess::ipcdetail::bool_<false>]’  
boost-build/include/boost/interprocess/detail/named_proxy.hpp:73:10:   required from ‘void boost::interprocess::ipcdetail::CtorArgN<T, is_iterator, Args>::construct_n(void*, std::size_t, std::size_t&) [with T = boost::container::vector<boost::geometry::model::point<double, 2, boost::geometry::cs::geographic<boost::geometry::degree> >, boost::interprocess::cached_adaptive_pool<boost::geometry::model::point<double, 2, boost::geometry::cs::geographic<boost::geometry::degree> >, boost::interprocess::segment_manager<char, boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family>, boost::interprocess::iset_index> > >; bool is_iterator = false; Args = {boost::interprocess::cached_adaptive_pool<boost::geometry::model::point<double, 2, boost::geometry::cs::geographic<boost::geometry::degree> >, boost::interprocess::segment_manager<char, boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family, boost::interprocess::offset_ptr<void, long int, long unsigned int, 0>, 0>, boost::interprocess::iset_index>, 64, 2, 5>&}; std::size_t = long unsigned int]’  
boost-build/include/boost/interprocess/detail/named_proxy.hpp:67:17:   required from here  
boost-build/include/boost/geometry/core/point_type.hpp:71:17: error: no type named ‘type’ in ‘struct boost::geometry::traits::point_type<boost::container::dtl::tuple<boost::interprocess::cached_adaptive_pool<boost::geometry::model::point<double, 2, boost::geometry::cs::geographic<boost::geometry::degree> >, boost::interprocess::segment_manager<char, boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family, boost::interprocess::offset_ptr<void, long int, long unsigned int, 0>, 0>, boost::interprocess::iset_index>, 64, 2, 5>&> >’  
   71 |         >::type type;  
      |                 ^~~~  
```  
  
The error comes from here (get() call in Line 85): \  
https://github.com/boostorg/interprocess/blob/a0c5a8ff176434c9024d4540ce092a2eebb8c5c3/include/boost/interprocess/detail/named_proxy.hpp#L85  
  
The get() method somehow is redirected to the boost::geometry get() method with a similar template syntax here: \  
https://github.com/boostorg/geometry/blob/dfcbb756021d1d5f4afa262413aa85515b9fd18e/include/boost/geometry/core/access.hpp#L268  
  
It does not make any sense for me, I have no idea why this redirection happens.  
  
Now for the fix of this issue: \  
When I add to the boost::interprocess get() call in Line 85 (and Line 81 above to be sure) the ```ipcdetail::``` namespace in front: \  
```ipcdetail::get<IdxPack>(args_)``` \  
then the compilation and execution works.  
  
I don't understand why the get() call is redirected to boost::geometry, maybe the ```using``` declaration from here is creating a namespace conflict: \  
https://github.com/boostorg/interprocess/blob/a0c5a8ff176434c9024d4540ce092a2eebb8c5c3/include/boost/interprocess/detail/variadic_templates_tools.hpp#L31  
By calling get() explicitly by prepending ```ipcdetail::``` the compilation works.  
  
By the way, prepending directly ```container::dtl::``` instead of ```ipcdetail::``` also works, of course! Maybe this would be even better. I think, the whole ```boost/interprocess/detail/variadic_templates_tools.hpp``` should be removed and all calls made explicit, just to be sure that no other namespace conflicts arise.  
  
Would it be possible to change the calls to the get() methods to the more explicit ones, so that there is no namespace conflict with the get() methods from boost::geometry?  
  
One very interesting aspect for me is the following: When I change in my code line 15 from  
```  
using point_type = boost::geometry::model::point<double, 2, boost::geometry::cs::geographic<boost::geometry::degree>>;  
```  
into  
```  
using point_type = boost::geometry::model::point<double, 2, boost::geometry::cs::cartesian>;  
```  
then the compilation also works. However, I need geographic coordinate system, so this workaround is no solution. I also don't really understand why this template change alters the result of the compilers.  
  
There might also be other such edge cases still unknown with different libraries having similar templated get() methods, resulting in similar namespace conflicts, but I only know of this one so far.  
  
EDIT:  
Another workaround that I found just now is using a non-templated wrapper object with move-construction, for example:  
```  
struct wrapper {  
    points_type data;  
};  
  
// replace construct line with the following  
auto *wrap = memory->find_or_construct<wrapper>("identifier")(wrapper{points_type{*allocator}});  
auto *data = &wrap->data;  
```  
This workaround now works in combination with the geographic setting, the get() method is not misrouted this way.  
  
Thank you for your assistance!

---

## Comment 1

> Username: addy90  
> Created at: 2023-09-20 15:34:42 UTC  
> Url: https://github.com/boostorg/interprocess/issues/195#issuecomment-1727966255  

Thank you for your fix earlier this year. However, I cannot find the fix in the current Boost 1.83 version (and also not 1.82), see for example: https://www.boost.org/doc/libs/1_83_0/doc/html/interprocess/acknowledgements_notes.html#interprocess.acknowledgements_notes.release_notes  
The release notes concerning this fix are missing. I would be glad if you could look into it!
