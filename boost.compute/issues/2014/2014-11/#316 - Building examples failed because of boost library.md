# #316 - Building examples failed because of boost library [Closed]

> Username: adamcavendish  
> Created at: 2014-11-28 18:50:32 UTC  
> Updated at: 2014-11-29 07:10:54 UTC  
> Closed at: 2014-11-29 07:10:54 UTC  
> Url: https://github.com/boostorg/compute/issues/316  

Building examples failed caused by boost align problem. I'm using gcc-4.9 and boost 1.56.  
May you tell me which version of boost are you using to build it?  
  
[3/46] Building CXX object example/CMakeFiles/amd_cpp_kernel.dir/amd_cpp_kernel.cpp.o  
FAILED: /usr/bin/c++    -isystem /usr/local/include -I../include -I../example/../include -MMD -MT example/CMakeFiles/amd_cpp_kernel.dir/amd_cpp_kernel.cpp.o -MF example/CMakeFiles/amd_cpp_kernel.dir/amd_cpp_kernel.cpp.o.d -o example/CMakeFiles/amd_cpp_kernel.dir/amd_cpp_kernel.cpp.o -c ../example/amd_cpp_kernel.cpp  
In file included from /usr/local/include/boost/align/align.hpp:25:0,  
                 from /usr/local/include/boost/smart_ptr/detail/array_allocator.hpp:12,  
                 from /usr/local/include/boost/smart_ptr/detail/array_count_impl.hpp:12,  
                 from /usr/local/include/boost/smart_ptr/make_shared_array.hpp:12,  
                 from /usr/local/include/boost/smart_ptr/make_shared.hpp:18,  
                 from /usr/local/include/boost/make_shared.hpp:15,  
                 from ../include/boost/compute/detail/program_cache.hpp:17,  
                 from ../include/boost/compute/detail/meta_kernel.hpp:39,  
                 from ../include/boost/compute/iterator/buffer_iterator.hpp:24,  
                 from ../include/boost/compute/algorithm/copy.hpp:23,  
                 from ../example/amd_cpp_kernel.cpp:18:  
/usr/local/include/boost/align/detail/align_cxx11.hpp:16:20: error: ‘std::align’ has not been declared  
         using std::align;  
                    ^  
In file included from /usr/local/include/boost/smart_ptr/detail/array_count_impl.hpp:12:0,  
                 from /usr/local/include/boost/smart_ptr/make_shared_array.hpp:12,  
                 from /usr/local/include/boost/smart_ptr/make_shared.hpp:18,  
                 from /usr/local/include/boost/make_shared.hpp:15,  
                 from ../include/boost/compute/detail/program_cache.hpp:17,  
                 from ../include/boost/compute/detail/meta_kernel.hpp:39,  
                 from ../include/boost/compute/iterator/buffer_iterator.hpp:24,  
                 from ../include/boost/compute/algorithm/copy.hpp:23,  
                 from ../example/amd_cpp_kernel.cpp:18:  
/usr/local/include/boost/smart_ptr/detail/array_allocator.hpp: In member function ‘boost::detail::as_allocator<A, T, R>::pointer boost::detail::as_allocator<A, T, R>::allocate(boost::detail::as_allocator<A, T, R>::size_type, boost::detail::as_allocator<A, T, R>::const_void_pointer)’:  
/usr/local/include/boost/smart_ptr/detail/array_allocator.hpp:138:23: error: ‘align’ is not a member of ‘boost::alignment’  
                 (void)boost::alignment::align(M, n2, p2, n3);  
                       ^  
/usr/local/include/boost/smart_ptr/detail/array_allocator.hpp: In member function ‘Y\* boost::detail::ms_allocator<T, Y>::allocate(boost::detail::ms_allocator<T, Y>::size_type, const void_)’:  
/usr/local/include/boost/smart_ptr/detail/array_allocator.hpp:247:23: error: ‘align’ is not a member of ‘boost::alignment’  
                 (void)boost::alignment::align(M, n2, p2, n3);  
                       ^  
[3/46] Building CXX object example/CMakeFiles/fizz_buzz.dir/fizz_buzz.cpp.o  
FAILED: /usr/bin/c++    -isystem /usr/local/include -I../include -I../example/../include -MMD -MT example/CMakeFiles/fizz_buzz.dir/fizz_buzz.cpp.o -MF example/CMakeFiles/fizz_buzz.dir/fizz_buzz.cpp.o.d -o example/CMakeFiles/fizz_buzz.dir/fizz_buzz.cpp.o -c ../example/fizz_buzz.cpp  
In file included from /usr/local/include/boost/align/align.hpp:25:0,  
                 from /usr/local/include/boost/smart_ptr/detail/array_allocator.hpp:12,  
                 from /usr/local/include/boost/smart_ptr/detail/array_count_impl.hpp:12,  
                 from /usr/local/include/boost/smart_ptr/make_shared_array.hpp:12,  
                 from /usr/local/include/boost/smart_ptr/make_shared.hpp:18,  
                 from /usr/local/include/boost/make_shared.hpp:15,  
                 from ../include/boost/compute/detail/program_cache.hpp:17,  
                 from ../include/boost/compute/detail/meta_kernel.hpp:39,  
                 from ../include/boost/compute/iterator/buffer_iterator.hpp:24,  
                 from ../include/boost/compute/algorithm/copy.hpp:23,  
                 from ../example/fizz_buzz.cpp:15:  
/usr/local/include/boost/align/detail/align_cxx11.hpp:16:20: error: ‘std::align’ has not been declared  
         using std::align;  
                    ^  
In file included from /usr/local/include/boost/smart_ptr/detail/array_count_impl.hpp:12:0,  
                 from /usr/local/include/boost/smart_ptr/make_shared_array.hpp:12,  
                 from /usr/local/include/boost/smart_ptr/make_shared.hpp:18,  
                 from /usr/local/include/boost/make_shared.hpp:15,  
                 from ../include/boost/compute/detail/program_cache.hpp:17,  
                 from ../include/boost/compute/detail/meta_kernel.hpp:39,  
                 from ../include/boost/compute/iterator/buffer_iterator.hpp:24,  
                 from ../include/boost/compute/algorithm/copy.hpp:23,  
                 from ../example/fizz_buzz.cpp:15:  
/usr/local/include/boost/smart_ptr/detail/array_allocator.hpp: In member function ‘boost::detail::as_allocator<A, T, R>::pointer boost::detail::as_allocator<A, T, R>::allocate(boost::detail::as_allocator<A, T, R>::size_type, boost::detail::as_allocator<A, T, R>::const_void_pointer)’:  
/usr/local/include/boost/smart_ptr/detail/array_allocator.hpp:138:23: error: ‘align’ is not a member of ‘boost::alignment’  
                 (void)boost::alignment::align(M, n2, p2, n3);  
                       ^  
/usr/local/include/boost/smart_ptr/detail/array_allocator.hpp: In member function ‘Y_ boost::detail::ms_allocator<T, Y>::allocate(boost::detail::ms_allocator<T, Y>::size_type, const void_)’:  
/usr/local/include/boost/smart_ptr/detail/array_allocator.hpp:247:23: error: ‘align’ is not a member of ‘boost::alignment’  
                 (void)boost::alignment::align(M, n2, p2, n3);  
                       ^  
[3/46] Building CXX object example/CMakeFiles/simple_moving_average.dir/simple_moving_average.cpp.o  
FAILED: /usr/bin/c++    -isystem /usr/local/include -I../include -I../example/../include -MMD -MT example/CMakeFiles/simple_moving_average.dir/simple_moving_average.cpp.o -MF example/CMakeFiles/simple_moving_average.dir/simple_moving_average.cpp.o.d -o example/CMakeFiles/simple_moving_average.dir/simple_moving_average.cpp.o -c ../example/simple_moving_average.cpp  
In file included from /usr/local/include/boost/align/align.hpp:25:0,  
                 from /usr/local/include/boost/smart_ptr/detail/array_allocator.hpp:12,  
                 from /usr/local/include/boost/smart_ptr/detail/array_count_impl.hpp:12,  
                 from /usr/local/include/boost/smart_ptr/make_shared_array.hpp:12,  
                 from /usr/local/include/boost/smart_ptr/make_shared.hpp:18,  
                 from /usr/local/include/boost/make_shared.hpp:15,  
                 from ../include/boost/compute/detail/program_cache.hpp:17,  
                 from ../include/boost/compute/detail/meta_kernel.hpp:39,  
                 from ../include/boost/compute/iterator/buffer_iterator.hpp:24,  
                 from ../include/boost/compute/algorithm/copy.hpp:23,  
                 from ../example/simple_moving_average.cpp:16:  
/usr/local/include/boost/align/detail/align_cxx11.hpp:16:20: error: ‘std::align’ has not been declared  
         using std::align;  
                    ^  
In file included from /usr/local/include/boost/smart_ptr/detail/array_count_impl.hpp:12:0,  
                 from /usr/local/include/boost/smart_ptr/make_shared_array.hpp:12,  
                 from /usr/local/include/boost/smart_ptr/make_shared.hpp:18,  
                 from /usr/local/include/boost/make_shared.hpp:15,  
                 from ../include/boost/compute/detail/program_cache.hpp:17,  
                 from ../include/boost/compute/detail/meta_kernel.hpp:39,  
                 from ../include/boost/compute/iterator/buffer_iterator.hpp:24,  
                 from ../include/boost/compute/algorithm/copy.hpp:23,  
                 from ../example/simple_moving_average.cpp:16:  
/usr/local/include/boost/smart_ptr/detail/array_allocator.hpp: In member function ‘boost::detail::as_allocator<A, T, R>::pointer boost::detail::as_allocator<A, T, R>::allocate(boost::detail::as_allocator<A, T, R>::size_type, boost::detail::as_allocator<A, T, R>::const_void_pointer)’:  
/usr/local/include/boost/smart_ptr/detail/array_allocator.hpp:138:23: error: ‘align’ is not a member of ‘boost::alignment’  
                 (void)boost::alignment::align(M, n2, p2, n3);  
                       ^  
/usr/local/include/boost/smart_ptr/detail/array_allocator.hpp: In member function ‘Y_ boost::detail::ms_allocator<T, Y>::allocate(boost::detail::ms_allocator<T, Y>::size_type, const void*)’:  
/usr/local/include/boost/smart_ptr/detail/array_allocator.hpp:247:23: error: ‘align’ is not a member of ‘boost::alignment’  
                 (void)boost::alignment::align(M, n2, p2, n3);

---

## Comment 1

> Username: kylelutz  
> Created at: 2014-11-28 19:22:41 UTC  
> Url: https://github.com/boostorg/compute/issues/316#issuecomment-64922921  

Interesting. On my main development machine I usually use Boost 1.54 and gcc-4.8 or clang-3.4 (though I've tested with a variety of other combinations).  
  
Are you compiling with C++11 enabled (i.e. `CMAKE_CXX_FLAGS=-std=c++11`)? Can you try gcc-4.8? This seems more like a bug with Boost.Align and/or Boost.SmartPtr. I'll look into it more.

---

## Comment 2

> Username: adamcavendish  
> Created at: 2014-11-29 05:33:16 UTC  
> Url: https://github.com/boostorg/compute/issues/316#issuecomment-64942315  

I switched to Boost 1.57 and examples built fine.

---

## Comment 3

> Username: adamcavendish  
> Created at: 2014-11-29 05:36:15 UTC  
> Url: https://github.com/boostorg/compute/issues/316#issuecomment-64942381  

However, if I added tests, it failed again.  
  
Flags:  
BOOST_COMPUTE_BUILD_BENCHMARKS:BOOL=ON  
BOOST_COMPUTE_BUILD_EXAMPLES:BOOL=ON  
BOOST_COMPUTE_BUILD_TESTS:BOOL=ON  
BOOST_COMPUTE_ENABLE_COVERAGE:BOOL=ON  
BOOST_COMPUTE_HAVE_EIGEN:BOOL=ON  
BOOST_COMPUTE_HAVE_OPENCV:BOOL=ON  
BOOST_COMPUTE_USE_CPP11:BOOL=ON  
  
FAILED: /usr/bin/c++   -DBOOST_TEST_DYN_LINK -std=c++11 -isystem /usr/local/include -I../include -I../test/../include -isystem /usr/local/include/eigen3 -isystem /usr/include/opencv    -fprofile-arcs -ftest-coverage -MMD -MT test/CMakeFiles/test_copy.dir/test_copy.cpp.o -MF test/CMakeFiles/test_copy.dir/test_copy.cpp.o.d -o test/CMakeFiles/test_copy.dir/test_copy.cpp.o -c ../test/test_copy.cpp  
In file included from ../test/test_copy.cpp:24:0:  
../include/boost/compute/algorithm/copy.hpp: In instantiation of ‘boost::compute::future<HostIterator> boost::compute::copy_async(InputIterator, InputIterator, OutputIterator, boost::compute::command_queue&) [with InputIterator = boost::compute::buffer_iterator<int>; OutputIterator = boost::compute::buffer_iterator<short int>]’:  
../test/test_copy.cpp:263:70:   required from here  
../include/boost/compute/algorithm/copy.hpp:354:66: error: no matching function for call to ‘dispatch_copy_async(boost::compute::buffer_iterator<int>&, boost::compute::buffer_iterator<int>&, boost::compute::buffer_iterator<short int>&, boost::compute::command_queue&)’  
     return detail::dispatch_copy_async(first, last, result, queue);  
                                                                  ^  
../include/boost/compute/algorithm/copy.hpp:354:66: note: candidates are:  
../include/boost/compute/algorithm/copy.hpp:62:1: note: template<class InputIterator, class OutputIterator> boost::compute::future<HostIterator> boost::compute::detail::dispatch_copy_async(InputIterator, InputIterator, OutputIterator, boost::compute::command_queue&, typename boost::enable_if_c<((! boost::compute::detail::is_device_iterator<InputIterator>::value) && boost::compute::detail::is_device_iterator<OutputIterator>::value)>::type_)  
 dispatch_copy_async(InputIterator first,  
 ^  
../include/boost/compute/algorithm/copy.hpp:62:1: note:   template argument deduction/substitution failed:  
../include/boost/compute/algorithm/copy.hpp: In substitution of ‘template<class InputIterator, class OutputIterator> boost::compute::future<HostIterator> boost::compute::detail::dispatch_copy_async(InputIterator, InputIterator, OutputIterator, boost::compute::command_queue&, typename boost::enable_if_c<((! boost::compute::detail::is_device_iterator<InputIterator>::value) && boost::compute::detail::is_device_iterator<OutputIterator>::value)>::type_) [with InputIterator = boost::compute::buffer_iterator<int>; OutputIterator = boost::compute::buffer_iterator<short int>]’:  
../include/boost/compute/algorithm/copy.hpp:354:66:   required from ‘boost::compute::future<HostIterator> boost::compute::copy_async(InputIterator, InputIterator, OutputIterator, boost::compute::command_queue&) [with InputIterator = boost::compute::buffer_iterator<int>; OutputIterator = boost::compute::buffer_iterator<short int>]’  
../test/test_copy.cpp:263:70:   required from here  
../include/boost/compute/algorithm/copy.hpp:62:1: error: no type named ‘type’ in ‘struct boost::enable_if_c<false, void>’  
../include/boost/compute/algorithm/copy.hpp: In instantiation of ‘boost::compute::future<HostIterator> boost::compute::copy_async(InputIterator, InputIterator, OutputIterator, boost::compute::command_queue&) [with InputIterator = boost::compute::buffer_iterator<int>; OutputIterator = boost::compute::buffer_iterator<short int>]’:  
../test/test_copy.cpp:263:70:   required from here  
../include/boost/compute/algorithm/copy.hpp:108:1: note: template<class InputIterator, class OutputIterator> boost::compute::future<HostIterator> boost::compute::detail::dispatch_copy_async(InputIterator, InputIterator, OutputIterator, boost::compute::command_queue&, typename boost::enable_if_c<(boost::compute::detail::is_device_iterator<InputIterator>::value && (! boost::compute::detail::is_device_iterator<OutputIterator>::value))>::type_)  
 dispatch_copy_async(InputIterator first,  
 ^  
../include/boost/compute/algorithm/copy.hpp:108:1: note:   template argument deduction/substitution failed:  
../include/boost/compute/algorithm/copy.hpp: In substitution of ‘template<class InputIterator, class OutputIterator> boost::compute::future<HostIterator> boost::compute::detail::dispatch_copy_async(InputIterator, InputIterator, OutputIterator, boost::compute::command_queue&, typename boost::enable_if_c<(boost::compute::detail::is_device_iterator<InputIterator>::value && (! boost::compute::detail::is_device_iterator<OutputIterator>::value))>::type_) [with InputIterator = boost::compute::buffer_iterator<int>; OutputIterator = boost::compute::buffer_iterator<short int>]’:  
../include/boost/compute/algorithm/copy.hpp:354:66:   required from ‘boost::compute::future<HostIterator> boost::compute::copy_async(InputIterator, InputIterator, OutputIterator, boost::compute::command_queue&) [with InputIterator = boost::compute::buffer_iterator<int>; OutputIterator = boost::compute::buffer_iterator<short int>]’  
../test/test_copy.cpp:263:70:   required from here  
../include/boost/compute/algorithm/copy.hpp:108:1: error: no type named ‘type’ in ‘struct boost::enable_if_c<false, void>’  
../include/boost/compute/algorithm/copy.hpp: In instantiation of ‘boost::compute::future<HostIterator> boost::compute::copy_async(InputIterator, InputIterator, OutputIterator, boost::compute::command_queue&) [with InputIterator = boost::compute::buffer_iterator<int>; OutputIterator = boost::compute::buffer_iterator<short int>]’:  
../test/test_copy.cpp:263:70:   required from here  
../include/boost/compute/algorithm/copy.hpp:195:1: note: template<class InputIterator, class OutputIterator> boost::compute::future<HostIterator> boost::compute::detail::dispatch_copy_async(InputIterator, InputIterator, OutputIterator, boost::compute::command_queue&, typename boost::enable_if_c<((boost::compute::detail::is_device_iterator<InputIterator>::value && boost::compute::detail::is_device_iterator<OutputIterator>::value) && (!((boost::is_same<InputIterator, boost::compute::buffer_iterator<typename Iterator::value_type> >::value && boost::is_same<OutputIterator, boost::compute::buffer_iterator<typename OutputIterator::value_type> >::value) && boost::is_same<typename InputIterator::value_type, typename OutputIterator::value_type>::value)))>::type_)  
 dispatch_copy_async(InputIterator first,  
 ^  
../include/boost/compute/algorithm/copy.hpp:195:1: note:   template argument deduction/substitution failed:  
../include/boost/compute/algorithm/copy.hpp:222:1: note: template<class InputIterator, class OutputIterator> boost::compute::future<HostIterator> boost::compute::detail::dispatch_copy_async(InputIterator, InputIterator, OutputIterator, boost::compute::command_queue&, typename boost::enable_if_c<((boost::is_same<InputIterator, boost::compute::buffer_iterator<typename Iterator::value_type> >::value && boost::is_same<OutputIterator, boost::compute::buffer_iterator<typename OutputIterator::value_type> >::value) && boost::is_same<typename InputIterator::value_type, typename OutputIterator::value_type>::value)>::type_)  
 dispatch_copy_async(InputIterator first,  
 ^  
../include/boost/compute/algorithm/copy.hpp:222:1: note:   template argument deduction/substitution failed:  
../include/boost/compute/algorithm/copy.hpp: In substitution of ‘template<class InputIterator, class OutputIterator> boost::compute::future<HostIterator> boost::compute::detail::dispatch_copy_async(InputIterator, InputIterator, OutputIterator, boost::compute::command_queue&, typename boost::enable_if_c<((boost::is_same<InputIterator, boost::compute::buffer_iterator<typename Iterator::value_type> >::value && boost::is_same<OutputIterator, boost::compute::buffer_iterator<typename OutputIterator::value_type> >::value) && boost::is_same<typename InputIterator::value_type, typename OutputIterator::value_type>::value)>::type*) [with InputIterator = boost::compute::buffer_iterator<int>; OutputIterator = boost::compute::buffer_iterator<short int>]’:  
../include/boost/compute/algorithm/copy.hpp:354:66:   required from ‘boost::compute::future<HostIterator> boost::compute::copy_async(InputIterator, InputIterator, OutputIterator, boost::compute::command_queue&) [with InputIterator = boost::compute::buffer_iterator<int>; OutputIterator = boost::compute::buffer_iterator<short int>]’  
../test/test_copy.cpp:263:70:   required from here  
../include/boost/compute/algorithm/copy.hpp:222:1: error: no type named ‘type’ in ‘struct boost::enable_if_c<false, void>’

---

## Comment 4

> Username: kylelutz  
> Created at: 2014-11-29 06:47:36 UTC  
> Url: https://github.com/boostorg/compute/issues/316#issuecomment-64943573  

Oh yeah, this was reported a few days ago (see issue #313). The fix is currently in the `develop` branch if you want to try it out. Otherwise, it should be merged to `master` in the next few days.

---

## Comment 5

> Username: adamcavendish  
> Created at: 2014-11-29 07:10:54 UTC  
> Url: https://github.com/boostorg/compute/issues/316#issuecomment-64943975  

Switched to develop and all tests built fine.
