# #204 - boost.interprocess seems to have a bug. [Closed]

> Username: 635716742  
> Created at: 2023-09-21 11:13:50 UTC  
> Updated at: 2024-07-05 08:13:52 UTC  
> Closed at: 2024-07-05 08:13:52 UTC  
> Url: https://github.com/boostorg/interprocess/issues/204  

I would like to use boost.interprocess in a way similar to the `"code"` below, but encountered a `compile error`.  
 Upon analysis, it seems that the problem arises from the STL having two partial specializations for the template `indirectly_readable_traits`, which require the parameter to either contain `value_type` or `element_type`. However, in `boost::interprocess::offset_ptr`, both `value_type` and `element_type` are present simultaneously, leading to template ambiguity during instantiation.   
I am hoping for a solution or guidance on how to correctly use boost.interprocess in conjunction with the STL.  
# code  
```  
    boost::interprocess::shared_memory_object::remove("shm_name");  
    boost::interprocess::managed_shared_memory segment(boost::interprocess::create_only, "shm_name", 65536);  
  
    boost::interprocess::allocator<int, boost::interprocess::managed_shared_memory::segment_manager> myAllocator(segment.get_segment_manager());  
    using KWVector = std::vector<int, boost::interprocess::allocator<int, boost::interprocess::managed_shared_memory::segment_manager>>;  
    KWVector* pMyVec = segment.construct<KWVector>("MyVector")(myAllocator);  
    pMyVec->push_back(12);  
    pMyVec->push_back(34);  
```  
  
# compile error  
```  
  
/usr/include/c++/10/bits/iterator_concepts.h: In substitution of ‘template<class _Tp> using __iter_value_t = typename std::__detail::__iter_traits_impl<_Tp, std::indirectly_readable_traits<_Iter> >::type::value_type [with _Tp = boost::interprocess::offset_ptr<int, long int, long unsigned int, 0>]’:  
/usr/include/c++/10/bits/iterator_concepts.h:259:11:   required by substitution of ‘template<class _Tp> using iter_value_t = std::__detail::__iter_value_t<typename std::remove_cv<typename std::remove_reference<_Tp>::type>::type> [with _Tp = boost::interprocess::offset_ptr<int, long int, long unsigned int, 0>]’  
/usr/include/c++/10/bits/stl_iterator.h:1301:13:   required from ‘class std::move_iterator<boost::interprocess::offset_ptr<int, long int, long unsigned int, 0> >’  
/usr/include/c++/10/bits/stl_uninitialized.h:347:3:   required from ‘_ForwardIterator std::__uninitialized_move_if_noexcept_a(_InputIterator, _InputIterator, _ForwardIterator, _Allocator&) [with _InputIterator = boost::interprocess::offset_ptr<int, long int, long unsigned int, 0>; _ForwardIterator = boost::interprocess::offset_ptr<int, long int, long unsigned int, 0>; _Allocator = boost::interprocess::allocator<int, boost::interprocess::segment_manager<char, boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family>, boost::interprocess::iset_index> >]’  
/usr/include/c++/10/bits/vector.tcc:474:3:   required from ‘void std::vector<_Tp, _Alloc>::_M_realloc_insert(std::vector<_Tp, _Alloc>::iterator, _Args&& ...) [with _Args = {int}; _Tp = int; _Alloc = boost::interprocess::allocator<int, boost::interprocess::segment_manager<char, boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family>, boost::interprocess::iset_index> >; std::vector<_Tp, _Alloc>::iterator = std::vector<int, boost::interprocess::allocator<int, boost::interprocess::segment_manager<char, boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family>, boost::interprocess::iset_index> > >::iterator]’  
/usr/include/c++/10/bits/vector.tcc:121:21:   required from ‘std::vector<_Tp, _Alloc>::reference std::vector<_Tp, _Alloc>::emplace_back(_Args&& ...) [with _Args = {int}; _Tp = int; _Alloc = boost::interprocess::allocator<int, boost::interprocess::segment_manager<char, boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family>, boost::interprocess::iset_index> >; std::vector<_Tp, _Alloc>::reference = int&]’  
/usr/include/c++/10/bits/stl_vector.h:1204:21:   required from ‘void std::vector<_Tp, _Alloc>::push_back(std::vector<_Tp, _Alloc>::value_type&&) [with _Tp = int; _Alloc = boost::interprocess::allocator<int, boost::interprocess::segment_manager<char, boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family>, boost::interprocess::iset_index> >; std::vector<_Tp, _Alloc>::value_type = int]’  
/mnt/d/src/ShareConfig/src/main.cpp:21:26:   required from here  
/usr/include/c++/10/bits/iterator_concepts.h:254:13: error: ambiguous template instantiation for ‘struct std::indirectly_readable_traits<boost::interprocess::offset_ptr<int, long int, long unsigned int, 0> >’  
  254 |       using __iter_value_t = typename  
      |             ^~~~~~~~~~~~~~  
/usr/include/c++/10/bits/iterator_concepts.h:242:12: note: candidates are: ‘template<class _Tp>  requires requires{typename _Tp::value_type;} struct std::indirectly_readable_traits<_Iter> [with _Tp = boost::interprocess::offset_ptr<int, long int, long unsigned int, 0>]’  
  242 |     struct indirectly_readable_traits<_Tp>  
      |            ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
/usr/include/c++/10/bits/iterator_concepts.h:247:12: note:                 ‘template<class _Tp>  requires requires{typename _Tp::element_type;} struct std::indirectly_readable_traits<_Iter> [with _Tp = boost::interprocess::offset_ptr<int, long int, long unsigned int, 0>]’  
  247 |     struct indirectly_readable_traits<_Tp>  
      |            ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
/usr/include/c++/10/bits/iterator_concepts.h:254:13: error: invalid use of incomplete type ‘std::__detail::__iter_traits<boost::interprocess::offset_ptr<int, long int, long unsigned int, 0>, std::indirectly_readable_traits<boost::interprocess::offset_ptr<int, long int, long unsigned int, 0> > >’ {aka ‘struct std::indirectly_readable_traits<boost::interprocess::offset_ptr<int, long int, long unsigned int, 0> >’}  
  254 |       using __iter_value_t = typename  
      |             ^~~~~~~~~~~~~~  
/usr/include/c++/10/bits/iterator_concepts.h:225:29: note: declaration of ‘std::__detail::__iter_traits<boost::interprocess::offset_ptr<int, long int, long unsigned int, 0>, std::indirectly_readable_traits<boost::interprocess::offset_ptr<int, long int, long unsigned int, 0> > >’ {aka ‘struct std::indirectly_readable_traits<boost::interprocess::offset_ptr<int, long int, long unsigned int, 0> >’}  
  225 |   template<typename> struct indirectly_readable_traits { };  
```  
# STL and boost sound code  
```  
c++ file iterator_concepts.h:  
  
  template<typename _Tp> requires requires { typename _Tp::value_type; }  
    struct indirectly_readable_traits<_Tp>  
    : __detail::__cond_value_type<typename _Tp::value_type>  
    { };  
  
  template<typename _Tp> requires requires { typename _Tp::element_type; }  
    struct indirectly_readable_traits<_Tp>  
    : __detail::__cond_value_type<typename _Tp::element_type>  
    { };  
  
boost file offset_ptr.hpp:  
  
   typedef PointedType                       element_type;  
   typedef PointedType *                     pointer;  
   typedef typename ipcdetail::  
      add_reference<PointedType>::type       reference;  
   typedef typename ipcdetail::  
      remove_volatile<typename ipcdetail::  
         remove_const<PointedType>::type  
            >::type                          value_type;  
   typedef DifferenceType                    difference_type;  
   typedef std::random_access_iterator_tag   iterator_category;  
   typedef OffsetType                        offset_type;  
  
```  
  
# My ENV:  
  
ubuntu20.04  
gcc (Ubuntu 10.3.0-1ubuntu1~20.04) 10.3.0  
boost version 1.71( I also used 1.83)  
c++version: c++2a

---

## Comment 1

> Username: addy90  
> Created at: 2023-10-25 12:32:23 UTC  
> Updated at: 2023-10-25 12:36:07 UTC  
> Url: https://github.com/boostorg/interprocess/issues/204#issuecomment-1779172974  

Instead of `std::vector` you have to use `boost::interprocess::vector`, please see https://www.boost.org/doc/libs/1_83_0/doc/html/interprocess/allocators_containers.html#interprocess.allocators_containers.containers_explained. This is because of incompatibilities such as the one you encountered. More about these and further issues with the STL containers are in the docs linked.

---

## Comment 2

> Username: pliniofpa  
> Created at: 2024-05-16 08:56:28 UTC  
> Url: https://github.com/boostorg/interprocess/issues/204#issuecomment-2114575502  

This issue should be closed now.
