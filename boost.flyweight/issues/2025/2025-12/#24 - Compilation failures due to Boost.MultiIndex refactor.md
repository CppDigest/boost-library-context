# #24 - Compilation failures due to Boost.MultiIndex refactor [Closed]

> Username: Lastique  
> Created at: 2025-12-20 21:14:21 UTC  
> Updated at: 2025-12-21 12:15:11 UTC  
> Closed at: 2025-12-21 12:15:11 UTC  
> Url: https://github.com/boostorg/flyweight/issues/24  

After Boost.MultiIndex have [switched](https://lists.boost.org/archives/list/boost@lists.boost.org/message/BCUVBQEF6TTUBTR3EBPQNKFVAMNCFAUP/) to C++11, Boost.Flyweight tests fail:  
  
```  
In file included from ../../../boost/flyweight.hpp:17,  
                 from test_init.cpp:15:  
../../../boost/flyweight/hashed_factory.hpp:44:24: error: expected template-name before ‘<’ token  
   44 |     boost::mpl::vector1<  
      |                        ^  
../../../boost/flyweight/hashed_factory.hpp:44:24: error: expected ‘{’ before ‘<’ token  
../../../boost/flyweight/hashed_factory.hpp:44:24: error: expected unqualified-id before ‘<’ token  
In file included from ../../../boost/type_traits/is_convertible.hpp:20,  
                 from ../../../boost/flyweight/flyweight.hpp:45,  
                 from ../../../boost/flyweight.hpp:16:  
../../../boost/multi_index/detail/node_type.hpp: In instantiation of ‘struct boost::multi_index::detail::multi_index_node_type<boost::flyweights::detail::refcounted_value<boost::flyweights::detail::default_value_policy<int>::rep_type, int>, boost::flyweights::hashed_factory_class<boost::flyweights::detail::refcounted_value<boost::flyweights::detail::default_value_policy<int>::rep_type, int>, int, mpl_::na, mpl_::na, mpl_::na>::index_list, std::allocator<boost::flyweights::detail::refcounted_value<boost::flyweights::detail::default_value_policy<int>::rep_type, int> > >’:  
../../../boost/multi_index_container.hpp:91:7:   required from ‘class boost::multi_index::multi_index_container<boost::flyweights::detail::refcounted_value<boost::flyweights::detail::default_value_policy<int>::rep_type, int>, boost::flyweights::hashed_factory_class<boost::flyweights::detail::refcounted_value<boost::flyweights::detail::default_value_policy<int>::rep_type, int>, int, mpl_::na, mpl_::na, mpl_::na>::index_list, std::allocator<boost::flyweights::detail::refcounted_value<boost::flyweights::detail::default_value_policy<int>::rep_type, int> > >’  
../../../boost/flyweight/hashed_factory.hpp:94:18:   required from ‘class boost::flyweights::hashed_factory_class<boost::flyweights::detail::refcounted_value<boost::flyweights::detail::default_value_policy<int>::rep_type, int>, int, mpl_::na, mpl_::na, mpl_::na>’  
test_init.cpp:20:8:   required from ‘struct marked_hashed_factory_class<(& boost_flyweight_test::mark1), boost::flyweights::detail::refcounted_value<boost::flyweights::detail::default_value_policy<int>::rep_type, int>, int>’  
../../../boost/flyweight/detail/flyweight_core.hpp:140:46:   required from ‘class boost::flyweights::detail::flyweight_core<boost::flyweights::detail::default_value_policy<int>, mpl_::na, boost::flyweights::refcounted, marked_hashed_factory<(& boost_flyweight_test::mark1)>, boost::flyweights::simple_locking, boost::flyweights::static_holder>’  
../../../boost/flyweight/flyweight.hpp:173:48:   required from ‘class boost::flyweights::flyweight<int, marked_hashed_factory<(& boost_flyweight_test::mark1)> >’  
test_init.cpp:38:57:   required from here  
../../../boost/multi_index/detail/node_type.hpp:40:66: error: static assertion failed: detail::is_index_list<IndexSpecifierList>::value  
   40 |   BOOST_STATIC_ASSERT(detail::is_index_list<IndexSpecifierList>::value);  
      |                                                                  ^~~~~  
../../../boost/static_assert.hpp:71:55: note: in definition of macro ‘BOOST_STATIC_ASSERT’  
   71 | #     define BOOST_STATIC_ASSERT( ... ) static_assert(__VA_ARGS__, #__VA_ARGS__)  
      |                                                       ^~~~~~~~~~~  
../../../boost/multi_index/detail/node_type.hpp:40:66: note: ‘std::integral_constant<bool, false>::value’ evaluates to false  
   40 |   BOOST_STATIC_ASSERT(detail::is_index_list<IndexSpecifierList>::value);  
      |                                                                  ^~~~~  
../../../boost/static_assert.hpp:71:55: note: in definition of macro ‘BOOST_STATIC_ASSERT’  
   71 | #     define BOOST_STATIC_ASSERT( ... ) static_assert(__VA_ARGS__, #__VA_ARGS__)  
      |                                                       ^~~~~~~~~~~  
In file included from ../../../boost/mp11/bind.hpp:11,  
                 from ../../../boost/parameter/aux_/is_placeholder.hpp:46,  
                 from ../../../boost/parameter/value_type.hpp:101,  
                 from ../../../boost/parameter/aux_/arg_list.hpp:1207,  
                 from ../../../boost/parameter/parameters.hpp:22,  
                 from ../../../boost/flyweight/detail/value_tag.hpp:17,  
                 from ../../../boost/flyweight/detail/default_value_policy.hpp:19,  
                 from ../../../boost/flyweight/flyweight.hpp:23:  
../../../boost/mp11/algorithm.hpp: In substitution of ‘template<class L, class V, template<class ...> class F> using boost::mp11::mp_reverse_fold = typename boost::mp11::detail::mp_reverse_fold_impl<typename boost::mp11::detail::mp_rename_impl<L, boost::mp11::mp_list>::type, V, F>::type [with L = boost::flyweights::hashed_factory_class<boost::flyweights::detail::refcounted_value<boost::flyweights::detail::default_value_policy<int>::rep_type, int>, int, mpl_::na, mpl_::na, mpl_::na>::index_list; V = boost::multi_index::detail::index_node_base<boost::flyweights::detail::refcounted_value<boost::flyweights::detail::default_value_policy<int>::rep_type, int>, std::allocator<boost::flyweights::detail::refcounted_value<boost::flyweights::detail::default_value_policy<int>::rep_type, int> > >; F = boost::multi_index::detail::node_type]’:  
../../../boost/multi_index/detail/node_type.hpp:46:5:   required from ‘struct boost::multi_index::detail::multi_index_node_type<boost::flyweights::detail::refcounted_value<boost::flyweights::detail::default_value_policy<int>::rep_type, int>, boost::flyweights::hashed_factory_class<boost::flyweights::detail::refcounted_value<boost::flyweights::detail::default_value_policy<int>::rep_type, int>, int, mpl_::na, mpl_::na, mpl_::na>::index_list, std::allocator<boost::flyweights::detail::refcounted_value<boost::flyweights::detail::default_value_policy<int>::rep_type, int> > >’  
../../../boost/multi_index_container.hpp:91:7:   required from ‘class boost::multi_index::multi_index_container<boost::flyweights::detail::refcounted_value<boost::flyweights::detail::default_value_policy<int>::rep_type, int>, boost::flyweights::hashed_factory_class<boost::flyweights::detail::refcounted_value<boost::flyweights::detail::default_value_policy<int>::rep_type, int>, int, mpl_::na, mpl_::na, mpl_::na>::index_list, std::allocator<boost::flyweights::detail::refcounted_value<boost::flyweights::detail::default_value_policy<int>::rep_type, int> > >’  
../../../boost/flyweight/hashed_factory.hpp:94:18:   required from ‘class boost::flyweights::hashed_factory_class<boost::flyweights::detail::refcounted_value<boost::flyweights::detail::default_value_policy<int>::rep_type, int>, int, mpl_::na, mpl_::na, mpl_::na>’  
test_init.cpp:20:8:   required from ‘struct marked_hashed_factory_class<(& boost_flyweight_test::mark1), boost::flyweights::detail::refcounted_value<boost::flyweights::detail::default_value_policy<int>::rep_type, int>, int>’  
../../../boost/flyweight/detail/flyweight_core.hpp:140:46:   required from ‘class boost::flyweights::detail::flyweight_core<boost::flyweights::detail::default_value_policy<int>, mpl_::na, boost::flyweights::refcounted, marked_hashed_factory<(& boost_flyweight_test::mark1)>, boost::flyweights::simple_locking, boost::flyweights::static_holder>’  
../../../boost/flyweight/flyweight.hpp:173:48:   required from ‘class boost::flyweights::flyweight<int, marked_hashed_factory<(& boost_flyweight_test::mark1)> >’  
test_init.cpp:38:57:   required from here  
../../../boost/mp11/algorithm.hpp:973:62: error: no type named ‘type’ in ‘struct boost::mp11::detail::mp_rename_impl<boost::flyweights::hashed_factory_class<boost::flyweights::detail::refcounted_value<boost::flyweights::detail::default_value_policy<int>::rep_type, int>, int, mpl_::na, mpl_::na, mpl_::na>::index_list, boost::mp11::mp_list>’  
  973 | template<class L, class V, template<class...> class F> using mp_reverse_fold = typename detail::mp_reverse_fold_impl<mp_rename<L, mp_list>, V, F>::type;  
      |                                                              ^~~~~~~~~~~~~~~  
In file included from ../../../boost/multi_index_container.hpp:29,  
                 from ../../../boost/flyweight/hashed_factory.hpp:19:  
../../../boost/multi_index/detail/base_type.hpp: In instantiation of ‘struct boost::multi_index::detail::nth_layer<0, boost::flyweights::detail::refcounted_value<boost::flyweights::detail::default_value_policy<int>::rep_type, int>, boost::flyweights::hashed_factory_class<boost::flyweights::detail::refcounted_value<boost::flyweights::detail::default_value_policy<int>::rep_type, int>, int, mpl_::na, mpl_::na, mpl_::na>::index_list, std::allocator<boost::flyweights::detail::refcounted_value<boost::flyweights::detail::default_value_policy<int>::rep_type, int> > >’:  
../../../boost/multi_index/detail/base_type.hpp:53:8:   required from ‘struct boost::multi_index::detail::multi_index_base_type<boost::flyweights::detail::refcounted_value<boost::flyweights::detail::default_value_policy<int>::rep_type, int>, boost::flyweights::hashed_factory_class<boost::flyweights::detail::refcounted_value<boost::flyweights::detail::default_value_policy<int>::rep_type, int>, int, mpl_::na, mpl_::na, mpl_::na>::index_list, std::allocator<boost::flyweights::detail::refcounted_value<boost::flyweights::detail::default_value_policy<int>::rep_type, int> > >’  
../../../boost/multi_index_container.hpp:91:7:   required from ‘class boost::multi_index::multi_index_container<boost::flyweights::detail::refcounted_value<boost::flyweights::detail::default_value_policy<int>::rep_type, int>, boost::flyweights::hashed_factory_class<boost::flyweights::detail::refcounted_value<boost::flyweights::detail::default_value_policy<int>::rep_type, int>, int, mpl_::na, mpl_::na, mpl_::na>::index_list, std::allocator<boost::flyweights::detail::refcounted_value<boost::flyweights::detail::default_value_policy<int>::rep_type, int> > >’  
../../../boost/flyweight/hashed_factory.hpp:94:18:   required from ‘class boost::flyweights::hashed_factory_class<boost::flyweights::detail::refcounted_value<boost::flyweights::detail::default_value_policy<int>::rep_type, int>, int, mpl_::na, mpl_::na, mpl_::na>’  
test_init.cpp:20:8:   required from ‘struct marked_hashed_factory_class<(& boost_flyweight_test::mark1), boost::flyweights::detail::refcounted_value<boost::flyweights::detail::default_value_policy<int>::rep_type, int>, int>’  
../../../boost/flyweight/detail/flyweight_core.hpp:140:46:   required from ‘class boost::flyweights::detail::flyweight_core<boost::flyweights::detail::default_value_policy<int>, mpl_::na, boost::flyweights::refcounted, marked_hashed_factory<(& boost_flyweight_test::mark1)>, boost::flyweights::simple_locking, boost::flyweights::static_holder>’  
../../../boost/flyweight/flyweight.hpp:173:48:   required from ‘class boost::flyweights::flyweight<int, marked_hashed_factory<(& boost_flyweight_test::mark1)> >’  
test_init.cpp:38:57:   required from here  
../../../boost/multi_index/detail/base_type.hpp:43:47: error: no type named ‘type’ in ‘struct boost::mp11::detail::mp_size_impl<boost::flyweights::hashed_factory_class<boost::flyweights::detail::refcounted_value<boost::flyweights::detail::default_value_policy<int>::rep_type, int>, int, mpl_::na, mpl_::na, mpl_::na>::index_list>’  
   43 |     N==mp11::mp_size<Mp11IndexSpecifierList>::value,  
      |                                               ^~~~~  
../../../boost/multi_index/detail/base_type.hpp: In instantiation of ‘struct boost::multi_index::detail::multi_index_base_type<boost::flyweights::detail::refcounted_value<boost::flyweights::detail::default_value_policy<int>::rep_type, int>, boost::flyweights::hashed_factory_class<boost::flyweights::detail::refcounted_value<boost::flyweights::detail::default_value_policy<int>::rep_type, int>, int, mpl_::na, mpl_::na, mpl_::na>::index_list, std::allocator<boost::flyweights::detail::refcounted_value<boost::flyweights::detail::default_value_policy<int>::rep_type, int> > >’:  
../../../boost/multi_index_container.hpp:91:7:   required from ‘class boost::multi_index::multi_index_container<boost::flyweights::detail::refcounted_value<boost::flyweights::detail::default_value_policy<int>::rep_type, int>, boost::flyweights::hashed_factory_class<boost::flyweights::detail::refcounted_value<boost::flyweights::detail::default_value_policy<int>::rep_type, int>, int, mpl_::na, mpl_::na, mpl_::na>::index_list, std::allocator<boost::flyweights::detail::refcounted_value<boost::flyweights::detail::default_value_policy<int>::rep_type, int> > >’  
../../../boost/flyweight/hashed_factory.hpp:94:18:   required from ‘class boost::flyweights::hashed_factory_class<boost::flyweights::detail::refcounted_value<boost::flyweights::detail::default_value_policy<int>::rep_type, int>, int, mpl_::na, mpl_::na, mpl_::na>’  
test_init.cpp:20:8:   required from ‘struct marked_hashed_factory_class<(& boost_flyweight_test::mark1), boost::flyweights::detail::refcounted_value<boost::flyweights::detail::default_value_policy<int>::rep_type, int>, int>’  
../../../boost/flyweight/detail/flyweight_core.hpp:140:46:   required from ‘class boost::flyweights::detail::flyweight_core<boost::flyweights::detail::default_value_policy<int>, mpl_::na, boost::flyweights::refcounted, marked_hashed_factory<(& boost_flyweight_test::mark1)>, boost::flyweights::simple_locking, boost::flyweights::static_holder>’  
../../../boost/flyweight/flyweight.hpp:173:48:   required from ‘class boost::flyweights::flyweight<int, marked_hashed_factory<(& boost_flyweight_test::mark1)> >’  
test_init.cpp:38:57:   required from here  
../../../boost/multi_index/detail/base_type.hpp:56:66: error: static assertion failed: detail::is_index_list<IndexSpecifierList>::value  
   56 |   BOOST_STATIC_ASSERT(detail::is_index_list<IndexSpecifierList>::value);  
      |                                                                  ^~~~~  
../../../boost/static_assert.hpp:71:55: note: in definition of macro ‘BOOST_STATIC_ASSERT’  
   71 | #     define BOOST_STATIC_ASSERT( ... ) static_assert(__VA_ARGS__, #__VA_ARGS__)  
      |                                                       ^~~~~~~~~~~  
../../../boost/multi_index/detail/base_type.hpp:56:66: note: ‘std::integral_constant<bool, false>::value’ evaluates to false  
   56 |   BOOST_STATIC_ASSERT(detail::is_index_list<IndexSpecifierList>::value);  
      |                                                                  ^~~~~  
../../../boost/static_assert.hpp:71:55: note: in definition of macro ‘BOOST_STATIC_ASSERT’  
   71 | #     define BOOST_STATIC_ASSERT( ... ) static_assert(__VA_ARGS__, #__VA_ARGS__)  
      |                                                       ^~~~~~~~~~~  
  
    "g++"   -std=gnu++17 -fvisibility-inlines-hidden -fPIC -m64 -pthread -O0 -fno-inline -Wall -g -fvisibility=hidden  -DBOOST_ALL_NO_LIB=1 -DBOOST_CONTAINER_DYN_LINK=1 -DBOOST_CONTAINER_NO_LIB=1   -I"../../.." -I"../../../libs/config/include" -I"/home/lastique/src/boost/libs/assert/include" -I"/home/lastique/src/boost/libs/bind/include" -I"/home/lastique/src/boost/libs/container/include" -I"/home/lastique/src/boost/libs/container_hash/include" -I"/home/lastique/src/boost/libs/core/include" -I"/home/lastique/src/boost/libs/describe/include" -I"/home/lastique/src/boost/libs/detail/include" -I"/home/lastique/src/boost/libs/flyweight/include" -I"/home/lastique/src/boost/libs/function/include" -I"/home/lastique/src/boost/libs/function_types/include" -I"/home/lastique/src/boost/libs/functional/include" -I"/home/lastique/src/boost/libs/fusion/include" -I"/home/lastique/src/boost/libs/integer/include" -I"/home/lastique/src/boost/libs/interprocess/include" -I"/home/lastique/src/boost/libs/intrusive/include" -I"/home/lastique/src/boost/libs/io/include" -I"/home/lastique/src/boost/libs/move/include" -I"/home/lastique/src/boost/libs/mp11/include" -I"/home/lastique/src/boost/libs/mpl/include" -I"/home/lastique/src/boost/libs/multi_index/include" -I"/home/lastique/src/boost/libs/optional/include" -I"/home/lastique/src/boost/libs/parameter/include" -I"/home/lastique/src/boost/libs/predef/include" -I"/home/lastique/src/boost/libs/preprocessor/include" -I"/home/lastique/src/boost/libs/smart_ptr/include" -I"/home/lastique/src/boost/libs/static_assert/include" -I"/home/lastique/src/boost/libs/throw_exception/include" -I"/home/lastique/src/boost/libs/tuple/include" -I"/home/lastique/src/boost/libs/type_traits/include" -I"/home/lastique/src/boost/libs/typeof/include" -I"/home/lastique/src/boost/libs/utility/include" -I"/home/lastique/src/boost/libs/winapi/include"  -c -o "../../../bin.v2/libs/flyweight/test/test_init.test/gcc-13/debug/x86_64/cxxstd-17-gnu/threading-multi/visibility-hidden/test_init.o" "test_init.cpp"  
```  
  
Full build log:  
  
[flyweight_build.log](https://github.com/user-attachments/files/24274611/flyweight_build.log)  
  
Compiled with:  
  
```  
b2 -j16 cxxstd=17-gnu  
```  
  
The tests passed on Boost 1.90.0.  
  
CC @joaquintides
