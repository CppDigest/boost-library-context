# #297 - BOOST_COMPUTE_BUILD_TESTS fail on OSX [Closed]

> Username: anirul  
> Created at: 2014-11-05 08:48:03 UTC  
> Updated at: 2014-11-06 08:17:04 UTC  
> Closed at: 2014-11-06 08:17:04 UTC  
> Url: https://github.com/boostorg/compute/issues/297  

on OSX 10.10  
  
```  
> mkdir build-make  
> cd build-make  
> cmake .. -DBOOST_COMPUTE_BUILD_TESTS=1  
-- The C compiler identification is AppleClang 6.0.0.6000054  
-- The CXX compiler identification is AppleClang 6.0.0.6000054  
-- Check for working C compiler: /usr/bin/cc  
-- Check for working C compiler: /usr/bin/cc -- works  
-- Detecting C compiler ABI info  
-- Detecting C compiler ABI info - done  
-- Check for working CXX compiler: /usr/bin/c++  
-- Check for working CXX compiler: /usr/bin/c++ -- works  
-- Detecting CXX compiler ABI info  
-- Detecting CXX compiler ABI info - done  
-- Found OpenCL: /System/Library/Frameworks/OpenCL.framework    
-- Boost version: 1.56.0  
-- Boost version: 1.56.0  
-- Found the following Boost libraries:  
--   unit_test_framework  
-- Configuring done  
-- Generating done  
-- Build files have been written to: /Users/anirul/git/compute/build-make  
```  
  
and then with make you get an error in building test_dynamic_bitset.  
  
```  
> make  
[...]  
Scanning dependencies of target test_dynamic_bitset  
[ 20%] Building CXX object test/CMakeFiles/test_dynamic_bitset.dir/test_dynamic_bitset.cpp.o  
In file included from /Users/anirul/git/compute/test/test_dynamic_bitset.cpp:14:  
In file included from /Users/anirul/git/compute/include/boost/compute/algorithm/copy.hpp:20:  
In file included from /Users/anirul/git/compute/include/boost/compute/system.hpp:24:  
In file included from /Users/anirul/git/compute/include/boost/compute/command_queue.hpp:24:  
In file included from /Users/anirul/git/compute/include/boost/compute/image2d.hpp:23:  
/Users/anirul/git/compute/include/boost/compute/type_traits/type_name.hpp:94:20: error: implicit instantiation of  
      undefined template 'boost::compute::detail::type_name_trait<unsigned long>'  
    return detail::type_name_trait<T>::value();  
                   ^  
/Users/anirul/git/compute/include/boost/compute/algorithm/detail/reduce_on_gpu.hpp:186:30: note: in instantiation of  
      function template specialization 'boost::compute::type_name<unsigned long>' requested here  
        "__global const " << type_name<T>() << " *block = input + offset + block_offset;\n" <<  
                             ^  
/Users/anirul/git/compute/include/boost/compute/algorithm/reduce.hpp:208:5: note: in instantiation of function template  
      specialization  
      'boost::compute::detail::reduce_on_gpu<boost::compute::transform_iterator<boost::compute::buffer_iterator<unsigned  
      long long>, boost::compute::popcount<unsigned long long> >, unsigned long, boost::compute::plus<unsigned long> >'  
      requested here  
    reduce_on_gpu(first, last, result, function, queue);  
    ^  
/Users/anirul/git/compute/include/boost/compute/algorithm/reduce.hpp:222:5: note: in instantiation of function template  
      specialization  
      'boost::compute::detail::dispatch_reduce<boost::compute::transform_iterator<boost::compute::buffer_iterator<unsigned  
      long long>, boost::compute::popcount<unsigned long long> >, unsigned long>' requested here  
    dispatch_reduce(first, last, tmp.begin(), function, queue);  
    ^  
/Users/anirul/git/compute/include/boost/compute/algorithm/reduce.hpp:270:13: note: in instantiation of function template  
      specialization  
      'boost::compute::detail::dispatch_reduce<boost::compute::transform_iterator<boost::compute::buffer_iterator<unsigned  
      long long>, boost::compute::popcount<unsigned long long> >, unsigned long *, unsigned long>' requested here  
    detail::dispatch_reduce(first, last, result, function, queue);  
            ^  
/Users/anirul/git/compute/include/boost/compute/algorithm/transform_reduce.hpp:45:23: note: in instantiation of function  
      template specialization  
      'boost::compute::reduce<boost::compute::transform_iterator<boost::compute::buffer_iterator<unsigned long long>,  
      boost::compute::popcount<unsigned long long> >, unsigned long *, boost::compute::plus<unsigned long> >' requested  
      here  
    ::boost::compute::reduce(  
                      ^  
/Users/anirul/git/compute/include/boost/compute/container/dynamic_bitset.hpp:101:9: note: in instantiation of function  
      template specialization 'boost::compute::transform_reduce<boost::compute::buffer_iterator<unsigned long long>,  
      unsigned long *, boost::compute::popcount<unsigned long long>, boost::compute::plus<unsigned long> >' requested  
      here  
        transform_reduce(  
        ^  
/Users/anirul/git/compute/test/test_dynamic_bitset.cpp:38:28: note: in instantiation of member function  
      'boost::compute::dynamic_bitset<unsigned long long, boost::compute::buffer_allocator<unsigned long long> >::count'  
      requested here  
    BOOST_CHECK_EQUAL(bits.count(queue), size_t(0));  
                           ^  
/opt/local/include/boost/test/test_tools.hpp:169:109: note: expanded from macro 'BOOST_CHECK_EQUAL'  
    BOOST_CHECK_WITH_ARGS_IMPL( ::boost::test_tools::tt_detail::equal_impl_frwd(), "", CHECK, CHECK_EQUAL, (L)(R) )  
                                                                                                            ^  
/opt/local/include/boost/test/test_tools.hpp:103:59: note: expanded from macro 'BOOST_CHECK_WITH_ARGS_IMPL'  
    BOOST_PP_SEQ_FOR_EACH( BOOST_TEST_PASS_ARG_INFO, '_', ARGS ) ); \  
                                                          ^  
/opt/local/include/boost/preprocessor/seq/for_each.hpp:26:80: note: expanded from macro 'BOOST_PP_SEQ_FOR_EACH'  
#    define BOOST_PP_SEQ_FOR_EACH(macro, data, seq) BOOST_PP_FOR((macro, data, seq (nil)), BOOST_PP_SEQ_FOR_EACH...  
                                                                               ^  
note: (skipping 10 expansions in backtrace; use -fmacro-backtrace-limit=0 to see all)  
/opt/local/include/boost/preprocessor/seq/elem.hpp:43:60: note: expanded from macro 'BOOST_PP_SEQ_ELEM_II'  
#    define BOOST_PP_SEQ_ELEM_II(im) BOOST_PP_SEQ_ELEM_III(im)  
                                                           ^  
/opt/local/include/boost/preprocessor/seq/elem.hpp:44:41: note: expanded from macro 'BOOST_PP_SEQ_ELEM_III'  
#    define BOOST_PP_SEQ_ELEM_III(x, _) x  
                                        ^  
/opt/local/include/boost/test/test_tools.hpp:97:52: note: expanded from macro 'BOOST_TEST_PASS_ARG_INFO'  
#define BOOST_TEST_PASS_ARG_INFO( r, data, arg ) , arg, BOOST_STRINGIZE( arg )  
                                                   ^  
/Users/anirul/git/compute/include/boost/compute/type_traits/type_name.hpp:24:8: note: template is declared here  
struct type_name_trait;  
       ^  
In file included from /Users/anirul/git/compute/test/test_dynamic_bitset.cpp:14:  
In file included from /Users/anirul/git/compute/include/boost/compute/algorithm/copy.hpp:23:  
In file included from /Users/anirul/git/compute/include/boost/compute/iterator/buffer_iterator.hpp:24:  
/Users/anirul/git/compute/include/boost/compute/detail/meta_kernel.hpp:456:19: error: no matching function for call to  
      'type_name'  
        stream << type_name<Type>();  
                  ^~~~~~~~~~~~~~~  
/Users/anirul/git/compute/include/boost/compute/detail/meta_kernel.hpp:472:16: note: in instantiation of function  
      template specialization 'boost::compute::detail::meta_kernel::type<unsigned long>' requested here  
        return type<T>() + " " + name;  
               ^  
/Users/anirul/git/compute/include/boost/compute/algorithm/detail/reduce_on_gpu.hpp:191:11: note: in instantiation of  
      function template specialization 'boost::compute::detail::meta_kernel::decl<unsigned long>' requested here  
        k.decl<T>("sum") << " = 0;\n" <<  
          ^  
/Users/anirul/git/compute/include/boost/compute/algorithm/reduce.hpp:208:5: note: in instantiation of function template  
      specialization  
      'boost::compute::detail::reduce_on_gpu<boost::compute::transform_iterator<boost::compute::buffer_iterator<unsigned  
      long long>, boost::compute::popcount<unsigned long long> >, unsigned long, boost::compute::plus<unsigned long> >'  
      requested here  
    reduce_on_gpu(first, last, result, function, queue);  
    ^  
/Users/anirul/git/compute/include/boost/compute/algorithm/reduce.hpp:222:5: note: in instantiation of function template  
      specialization  
      'boost::compute::detail::dispatch_reduce<boost::compute::transform_iterator<boost::compute::buffer_iterator<unsigned  
      long long>, boost::compute::popcount<unsigned long long> >, unsigned long>' requested here  
    dispatch_reduce(first, last, tmp.begin(), function, queue);  
    ^  
/Users/anirul/git/compute/include/boost/compute/algorithm/reduce.hpp:270:13: note: in instantiation of function template  
      specialization  
      'boost::compute::detail::dispatch_reduce<boost::compute::transform_iterator<boost::compute::buffer_iterator<unsigned  
      long long>, boost::compute::popcount<unsigned long long> >, unsigned long *, unsigned long>' requested here  
    detail::dispatch_reduce(first, last, result, function, queue);  
            ^  
/Users/anirul/git/compute/include/boost/compute/algorithm/transform_reduce.hpp:45:23: note: in instantiation of function  
      template specialization  
      'boost::compute::reduce<boost::compute::transform_iterator<boost::compute::buffer_iterator<unsigned long long>,  
      boost::compute::popcount<unsigned long long> >, unsigned long *, boost::compute::plus<unsigned long> >' requested  
      here  
    ::boost::compute::reduce(  
                      ^  
/Users/anirul/git/compute/include/boost/compute/container/dynamic_bitset.hpp:101:9: note: in instantiation of function  
      template specialization 'boost::compute::transform_reduce<boost::compute::buffer_iterator<unsigned long long>,  
      unsigned long *, boost::compute::popcount<unsigned long long>, boost::compute::plus<unsigned long> >' requested  
      here  
        transform_reduce(  
        ^  
/Users/anirul/git/compute/test/test_dynamic_bitset.cpp:38:28: note: in instantiation of member function  
      'boost::compute::dynamic_bitset<unsigned long long, boost::compute::buffer_allocator<unsigned long long> >::count'  
      requested here  
    BOOST_CHECK_EQUAL(bits.count(queue), size_t(0));  
                           ^  
/opt/local/include/boost/test/test_tools.hpp:169:109: note: expanded from macro 'BOOST_CHECK_EQUAL'  
    BOOST_CHECK_WITH_ARGS_IMPL( ::boost::test_tools::tt_detail::equal_impl_frwd(), "", CHECK, CHECK_EQUAL, (L)(R) )  
                                                                                                            ^  
/opt/local/include/boost/test/test_tools.hpp:103:59: note: expanded from macro 'BOOST_CHECK_WITH_ARGS_IMPL'  
    BOOST_PP_SEQ_FOR_EACH( BOOST_TEST_PASS_ARG_INFO, '_', ARGS ) ); \  
                                                          ^  
/opt/local/include/boost/preprocessor/seq/for_each.hpp:26:80: note: expanded from macro 'BOOST_PP_SEQ_FOR_EACH'  
#    define BOOST_PP_SEQ_FOR_EACH(macro, data, seq) BOOST_PP_FOR((macro, data, seq (nil)), BOOST_PP_SEQ_FOR_EACH...  
                                                                               ^  
note: (skipping 10 expansions in backtrace; use -fmacro-backtrace-limit=0 to see all)  
/opt/local/include/boost/preprocessor/seq/elem.hpp:43:60: note: expanded from macro 'BOOST_PP_SEQ_ELEM_II'  
#    define BOOST_PP_SEQ_ELEM_II(im) BOOST_PP_SEQ_ELEM_III(im)  
                                                           ^  
/opt/local/include/boost/preprocessor/seq/elem.hpp:44:41: note: expanded from macro 'BOOST_PP_SEQ_ELEM_III'  
#    define BOOST_PP_SEQ_ELEM_III(x, _) x  
                                        ^  
/opt/local/include/boost/test/test_tools.hpp:97:52: note: expanded from macro 'BOOST_TEST_PASS_ARG_INFO'  
#define BOOST_TEST_PASS_ARG_INFO( r, data, arg ) , arg, BOOST_STRINGIZE( arg )  
                                                   ^  
/Users/anirul/git/compute/include/boost/compute/type_traits/type_name.hpp:92:20: note: candidate template ignored:  
      substitution failure [with T = unsigned long]  
inline const char* type_name()  
                   ^  
In file included from /Users/anirul/git/compute/test/test_dynamic_bitset.cpp:15:  
In file included from /Users/anirul/git/compute/include/boost/compute/container/dynamic_bitset.hpp:17:  
In file included from /Users/anirul/git/compute/include/boost/compute/algorithm/transform_reduce.hpp:15:  
In file included from /Users/anirul/git/compute/include/boost/compute/algorithm/reduce.hpp:26:  
/Users/anirul/git/compute/include/boost/compute/algorithm/detail/reduce_on_gpu.hpp:75:36: error: no matching function  
      for call to 'type_name'  
            "volatile __local " << type_name<T>() << " *lmem = scratch;\n" <<  
                                   ^~~~~~~~~~~~  
/Users/anirul/git/compute/include/boost/compute/algorithm/detail/reduce_on_gpu.hpp:202:34: note: in instantiation of  
      member function 'boost::compute::detail::ReduceBody<unsigned long, true>::body' requested here  
        k << ReduceBody<T,true>::body();  
                                 ^  
/Users/anirul/git/compute/include/boost/compute/algorithm/reduce.hpp:208:5: note: in instantiation of function template  
      specialization  
      'boost::compute::detail::reduce_on_gpu<boost::compute::transform_iterator<boost::compute::buffer_iterator<unsigned  
      long long>, boost::compute::popcount<unsigned long long> >, unsigned long, boost::compute::plus<unsigned long> >'  
      requested here  
    reduce_on_gpu(first, last, result, function, queue);  
    ^  
/Users/anirul/git/compute/include/boost/compute/algorithm/reduce.hpp:222:5: note: in instantiation of function template  
      specialization  
      'boost::compute::detail::dispatch_reduce<boost::compute::transform_iterator<boost::compute::buffer_iterator<unsigned  
      long long>, boost::compute::popcount<unsigned long long> >, unsigned long>' requested here  
    dispatch_reduce(first, last, tmp.begin(), function, queue);  
    ^  
/Users/anirul/git/compute/include/boost/compute/algorithm/reduce.hpp:270:13: note: in instantiation of function template  
      specialization  
      'boost::compute::detail::dispatch_reduce<boost::compute::transform_iterator<boost::compute::buffer_iterator<unsigned  
      long long>, boost::compute::popcount<unsigned long long> >, unsigned long *, unsigned long>' requested here  
    detail::dispatch_reduce(first, last, result, function, queue);  
            ^  
/Users/anirul/git/compute/include/boost/compute/algorithm/transform_reduce.hpp:45:23: note: in instantiation of function  
      template specialization  
      'boost::compute::reduce<boost::compute::transform_iterator<boost::compute::buffer_iterator<unsigned long long>,  
      boost::compute::popcount<unsigned long long> >, unsigned long *, boost::compute::plus<unsigned long> >' requested  
      here  
    ::boost::compute::reduce(  
                      ^  
/Users/anirul/git/compute/include/boost/compute/container/dynamic_bitset.hpp:101:9: note: in instantiation of function  
      template specialization 'boost::compute::transform_reduce<boost::compute::buffer_iterator<unsigned long long>,  
      unsigned long *, boost::compute::popcount<unsigned long long>, boost::compute::plus<unsigned long> >' requested  
      here  
        transform_reduce(  
        ^  
/Users/anirul/git/compute/test/test_dynamic_bitset.cpp:38:28: note: in instantiation of member function  
      'boost::compute::dynamic_bitset<unsigned long long, boost::compute::buffer_allocator<unsigned long long> >::count'  
      requested here  
    BOOST_CHECK_EQUAL(bits.count(queue), size_t(0));  
                           ^  
/opt/local/include/boost/test/test_tools.hpp:169:109: note: expanded from macro 'BOOST_CHECK_EQUAL'  
    BOOST_CHECK_WITH_ARGS_IMPL( ::boost::test_tools::tt_detail::equal_impl_frwd(), "", CHECK, CHECK_EQUAL, (L)(R) )  
                                                                                                            ^  
/opt/local/include/boost/test/test_tools.hpp:103:59: note: expanded from macro 'BOOST_CHECK_WITH_ARGS_IMPL'  
    BOOST_PP_SEQ_FOR_EACH( BOOST_TEST_PASS_ARG_INFO, '_', ARGS ) ); \  
                                                          ^  
/opt/local/include/boost/preprocessor/seq/for_each.hpp:26:80: note: expanded from macro 'BOOST_PP_SEQ_FOR_EACH'  
#    define BOOST_PP_SEQ_FOR_EACH(macro, data, seq) BOOST_PP_FOR((macro, data, seq (nil)), BOOST_PP_SEQ_FOR_EACH...  
                                                                               ^  
note: (skipping 10 expansions in backtrace; use -fmacro-backtrace-limit=0 to see all)  
/opt/local/include/boost/preprocessor/seq/elem.hpp:43:60: note: expanded from macro 'BOOST_PP_SEQ_ELEM_II'  
#    define BOOST_PP_SEQ_ELEM_II(im) BOOST_PP_SEQ_ELEM_III(im)  
                                                           ^  
/opt/local/include/boost/preprocessor/seq/elem.hpp:44:41: note: expanded from macro 'BOOST_PP_SEQ_ELEM_III'  
#    define BOOST_PP_SEQ_ELEM_III(x, _) x  
                                        ^  
/opt/local/include/boost/test/test_tools.hpp:97:52: note: expanded from macro 'BOOST_TEST_PASS_ARG_INFO'  
#define BOOST_TEST_PASS_ARG_INFO( r, data, arg ) , arg, BOOST_STRINGIZE( arg )  
                                                   ^  
/Users/anirul/git/compute/include/boost/compute/type_traits/type_name.hpp:92:20: note: candidate template ignored:  
      substitution failure [with T = unsigned long]  
inline const char* type_name()  
                   ^  
In file included from /Users/anirul/git/compute/test/test_dynamic_bitset.cpp:15:  
In file included from /Users/anirul/git/compute/include/boost/compute/container/dynamic_bitset.hpp:17:  
In file included from /Users/anirul/git/compute/include/boost/compute/algorithm/transform_reduce.hpp:15:  
In file included from /Users/anirul/git/compute/include/boost/compute/algorithm/reduce.hpp:26:  
/Users/anirul/git/compute/include/boost/compute/algorithm/detail/reduce_on_gpu.hpp:112:23: error: no matching function  
      for call to 'type_name'  
        "__local " << type_name<T>() << " scratch[TPB];\n" <<  
                      ^~~~~~~~~~~~  
/Users/anirul/git/compute/include/boost/compute/algorithm/detail/reduce_on_gpu.hpp:237:5: note: in instantiation of  
      function template specialization  
      'boost::compute::detail::initial_reduce<boost::compute::transform_iterator<boost::compute::buffer_iterator<unsigned  
      long long>, boost::compute::popcount<unsigned long long> >, boost::compute::plus<unsigned long> >' requested here  
    initial_reduce(first, last, ping, function, reduce_kernel, vpt, tpb, queue);  
    ^  
/Users/anirul/git/compute/include/boost/compute/algorithm/reduce.hpp:208:5: note: in instantiation of function template  
      specialization  
      'boost::compute::detail::reduce_on_gpu<boost::compute::transform_iterator<boost::compute::buffer_iterator<unsigned  
      long long>, boost::compute::popcount<unsigned long long> >, unsigned long, boost::compute::plus<unsigned long> >'  
      requested here  
    reduce_on_gpu(first, last, result, function, queue);  
    ^  
/Users/anirul/git/compute/include/boost/compute/algorithm/reduce.hpp:222:5: note: in instantiation of function template  
      specialization  
      'boost::compute::detail::dispatch_reduce<boost::compute::transform_iterator<boost::compute::buffer_iterator<unsigned  
      long long>, boost::compute::popcount<unsigned long long> >, unsigned long>' requested here  
    dispatch_reduce(first, last, tmp.begin(), function, queue);  
    ^  
/Users/anirul/git/compute/include/boost/compute/algorithm/reduce.hpp:270:13: note: in instantiation of function template  
      specialization  
      'boost::compute::detail::dispatch_reduce<boost::compute::transform_iterator<boost::compute::buffer_iterator<unsigned  
      long long>, boost::compute::popcount<unsigned long long> >, unsigned long *, unsigned long>' requested here  
    detail::dispatch_reduce(first, last, result, function, queue);  
            ^  
/Users/anirul/git/compute/include/boost/compute/algorithm/transform_reduce.hpp:45:23: note: in instantiation of function  
      template specialization  
      'boost::compute::reduce<boost::compute::transform_iterator<boost::compute::buffer_iterator<unsigned long long>,  
      boost::compute::popcount<unsigned long long> >, unsigned long *, boost::compute::plus<unsigned long> >' requested  
      here  
    ::boost::compute::reduce(  
                      ^  
/Users/anirul/git/compute/include/boost/compute/container/dynamic_bitset.hpp:101:9: note: in instantiation of function  
      template specialization 'boost::compute::transform_reduce<boost::compute::buffer_iterator<unsigned long long>,  
      unsigned long *, boost::compute::popcount<unsigned long long>, boost::compute::plus<unsigned long> >' requested  
      here  
        transform_reduce(  
        ^  
/Users/anirul/git/compute/test/test_dynamic_bitset.cpp:38:28: note: in instantiation of member function  
      'boost::compute::dynamic_bitset<unsigned long long, boost::compute::buffer_allocator<unsigned long long> >::count'  
      requested here  
    BOOST_CHECK_EQUAL(bits.count(queue), size_t(0));  
                           ^  
/opt/local/include/boost/test/test_tools.hpp:169:109: note: expanded from macro 'BOOST_CHECK_EQUAL'  
    BOOST_CHECK_WITH_ARGS_IMPL( ::boost::test_tools::tt_detail::equal_impl_frwd(), "", CHECK, CHECK_EQUAL, (L)(R) )  
                                                                                                            ^  
/opt/local/include/boost/test/test_tools.hpp:103:59: note: expanded from macro 'BOOST_CHECK_WITH_ARGS_IMPL'  
    BOOST_PP_SEQ_FOR_EACH( BOOST_TEST_PASS_ARG_INFO, '_', ARGS ) ); \  
                                                          ^  
/opt/local/include/boost/preprocessor/seq/for_each.hpp:26:80: note: expanded from macro 'BOOST_PP_SEQ_FOR_EACH'  
#    define BOOST_PP_SEQ_FOR_EACH(macro, data, seq) BOOST_PP_FOR((macro, data, seq (nil)), BOOST_PP_SEQ_FOR_EACH...  
                                                                               ^  
note: (skipping 10 expansions in backtrace; use -fmacro-backtrace-limit=0 to see all)  
/opt/local/include/boost/preprocessor/seq/elem.hpp:43:60: note: expanded from macro 'BOOST_PP_SEQ_ELEM_II'  
#    define BOOST_PP_SEQ_ELEM_II(im) BOOST_PP_SEQ_ELEM_III(im)  
                                                           ^  
/opt/local/include/boost/preprocessor/seq/elem.hpp:44:41: note: expanded from macro 'BOOST_PP_SEQ_ELEM_III'  
#    define BOOST_PP_SEQ_ELEM_III(x, _) x  
                                        ^  
/opt/local/include/boost/test/test_tools.hpp:97:52: note: expanded from macro 'BOOST_TEST_PASS_ARG_INFO'  
#define BOOST_TEST_PASS_ARG_INFO( r, data, arg ) , arg, BOOST_STRINGIZE( arg )  
                                                   ^  
/Users/anirul/git/compute/include/boost/compute/type_traits/type_name.hpp:92:20: note: candidate template ignored:  
      substitution failure [with T = unsigned long]  
inline const char* type_name()  
                   ^  
4 errors generated.  
make[2]: *** [test/CMakeFiles/test_dynamic_bitset.dir/test_dynamic_bitset.cpp.o] Error 1  
make[1]: *** [test/CMakeFiles/test_dynamic_bitset.dir/all] Error 2  
make: *** [all] Error 2  
```  
  
Any hint?

---

## Comment 1

> Username: kylelutz  
> Created at: 2014-11-05 16:14:48 UTC  
> Url: https://github.com/boostorg/compute/issues/297#issuecomment-61832816  

I think PR #298 should fix this. Could you test it out?

---

## Comment 2

> Username: anirul  
> Created at: 2014-11-06 08:17:04 UTC  
> Url: https://github.com/boostorg/compute/issues/297#issuecomment-61940729  

I moved to the develop branch and it worked great!  
  
```  
> git branch develop  
> cmake .. -DBOOST_COMPUTE_BUILD_EXAMPLES=1 -DBOOST_COMPUTE_HAVE_OPENCV=1 -DBOOST_COMPUTE_BUILD_TESTS=1  
```  
  
build  
  
```  
> make  
[...]  
[100%] Built target vector_addition  
```  
  
Thanks!
