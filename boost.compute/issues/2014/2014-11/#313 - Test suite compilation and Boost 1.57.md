# #313 - Test suite compilation and Boost 1.57 [Closed]

> Username: f-koehler  
> Created at: 2014-11-25 17:11:18 UTC  
> Updated at: 2014-11-26 15:15:56 UTC  
> Closed at: 2014-11-26 15:15:56 UTC  
> Url: https://github.com/boostorg/compute/issues/313  

I have encountered a problem compiling the test suit (might just be me, because of using boost 1.57). I had no time to take a closer look at this longish log yet, I just wanted to let you now.  
  
```  
In file included from /home/fabian/code/compute/test/test_copy.cpp:24:0:  
/home/fabian/code/compute/include/boost/compute/algorithm/copy.hpp: In instantiation of ‘boost::compute::future<HostIterator> boost::compute::copy_async(InputIterator, InputIterator, OutputIterator, boost::compute::command_queue&) [with InputIterator = boost::compute::buffer_iterator<int>; OutputIterator = boost::compute::buffer_iterator<short int>]’:  
/home/fabian/code/compute/test/test_copy.cpp:263:70:   required from here  
/home/fabian/code/compute/include/boost/compute/algorithm/copy.hpp:354:66: error: no matching function for call to ‘dispatch_copy_async(boost::compute::buffer_iterator<int>&, boost::compute::buffer_iterator<int>&, boost::compute::buffer_iterator<short int>&, boost::compute::command_queue&)’  
     return detail::dispatch_copy_async(first, last, result, queue);  
                                                                  ^  
/home/fabian/code/compute/include/boost/compute/algorithm/copy.hpp:354:66: note: candidates are:  
/home/fabian/code/compute/include/boost/compute/algorithm/copy.hpp:62:1: note: template<class InputIterator, class OutputIterator> boost::compute::future<HostIterator> boost::compute::detail::dispatch_copy_async(InputIterator, InputIterator, OutputIterator, boost::compute::command_queue&, typename boost::enable_if_c<((! boost::compute::detail::is_device_iterator<InputIterator>::value) && boost::compute::detail::is_device_iterator<OutputIterator>::value)>::type*)  
 dispatch_copy_async(InputIterator first,  
 ^  
/home/fabian/code/compute/include/boost/compute/algorithm/copy.hpp:62:1: note:   template argument deduction/substitution failed:  
/home/fabian/code/compute/include/boost/compute/algorithm/copy.hpp: In substitution of ‘template<class InputIterator, class OutputIterator> boost::compute::future<HostIterator> boost::compute::detail::dispatch_copy_async(InputIterator, InputIterator, OutputIterator, boost::compute::command_queue&, typename boost::enable_if_c<((! boost::compute::detail::is_device_iterator<InputIterator>::value) && boost::compute::detail::is_device_iterator<OutputIterator>::value)>::type*) [with InputIterator = boost::compute::buffer_iterator<int>; OutputIterator = boost::compute::buffer_iterator<short int>]’:  
/home/fabian/code/compute/include/boost/compute/algorithm/copy.hpp:354:66:   required from ‘boost::compute::future<HostIterator> boost::compute::copy_async(InputIterator, InputIterator, OutputIterator, boost::compute::command_queue&) [with InputIterator = boost::compute::buffer_iterator<int>; OutputIterator = boost::compute::buffer_iterator<short int>]’  
/home/fabian/code/compute/test/test_copy.cpp:263:70:   required from here  
/home/fabian/code/compute/include/boost/compute/algorithm/copy.hpp:62:1: error: no type named ‘type’ in ‘struct boost::enable_if_c<false, void>’  
/home/fabian/code/compute/include/boost/compute/algorithm/copy.hpp: In instantiation of ‘boost::compute::future<HostIterator> boost::compute::copy_async(InputIterator, InputIterator, OutputIterator, boost::compute::command_queue&) [with InputIterator = boost::compute::buffer_iterator<int>; OutputIterator = boost::compute::buffer_iterator<short int>]’:  
/home/fabian/code/compute/test/test_copy.cpp:263:70:   required from here  
/home/fabian/code/compute/include/boost/compute/algorithm/copy.hpp:108:1: note: template<class InputIterator, class OutputIterator> boost::compute::future<HostIterator> boost::compute::detail::dispatch_copy_async(InputIterator, InputIterator, OutputIterator, boost::compute::command_queue&, typename boost::enable_if_c<(boost::compute::detail::is_device_iterator<InputIterator>::value && (! boost::compute::detail::is_device_iterator<OutputIterator>::value))>::type*)  
 dispatch_copy_async(InputIterator first,  
 ^  
/home/fabian/code/compute/include/boost/compute/algorithm/copy.hpp:108:1: note:   template argument deduction/substitution failed:  
/home/fabian/code/compute/include/boost/compute/algorithm/copy.hpp: In substitution of ‘template<class InputIterator, class OutputIterator> boost::compute::future<HostIterator> boost::compute::detail::dispatch_copy_async(InputIterator, InputIterator, OutputIterator, boost::compute::command_queue&, typename boost::enable_if_c<(boost::compute::detail::is_device_iterator<InputIterator>::value && (! boost::compute::detail::is_device_iterator<OutputIterator>::value))>::type*) [with InputIterator = boost::compute::buffer_iterator<int>; OutputIterator = boost::compute::buffer_iterator<short int>]’:  
/home/fabian/code/compute/include/boost/compute/algorithm/copy.hpp:354:66:   required from ‘boost::compute::future<HostIterator> boost::compute::copy_async(InputIterator, InputIterator, OutputIterator, boost::compute::command_queue&) [with InputIterator = boost::compute::buffer_iterator<int>; OutputIterator = boost::compute::buffer_iterator<short int>]’  
/home/fabian/code/compute/test/test_copy.cpp:263:70:   required from here  
/home/fabian/code/compute/include/boost/compute/algorithm/copy.hpp:108:1: error: no type named ‘type’ in ‘struct boost::enable_if_c<false, void>’  
/home/fabian/code/compute/include/boost/compute/algorithm/copy.hpp: In instantiation of ‘boost::compute::future<HostIterator> boost::compute::copy_async(InputIterator, InputIterator, OutputIterator, boost::compute::command_queue&) [with InputIterator = boost::compute::buffer_iterator<int>; OutputIterator = boost::compute::buffer_iterator<short int>]’:  
/home/fabian/code/compute/test/test_copy.cpp:263:70:   required from here  
/home/fabian/code/compute/include/boost/compute/algorithm/copy.hpp:195:1: note: template<class InputIterator, class OutputIterator> boost::compute::future<HostIterator> boost::compute::detail::dispatch_copy_async(InputIterator, InputIterator, OutputIterator, boost::compute::command_queue&, typename boost::enable_if_c<((boost::compute::detail::is_device_iterator<InputIterator>::value && boost::compute::detail::is_device_iterator<OutputIterator>::value) && (!((boost::is_same<InputIterator, boost::compute::buffer_iterator<typename Iterator::value_type> >::value && boost::is_same<OutputIterator, boost::compute::buffer_iterator<typename OutputIterator::value_type> >::value) && boost::is_same<typename InputIterator::value_type, typename OutputIterator::value_type>::value)))>::type*)  
 dispatch_copy_async(InputIterator first,  
 ^  
/home/fabian/code/compute/include/boost/compute/algorithm/copy.hpp:195:1: note:   template argument deduction/substitution failed:  
/home/fabian/code/compute/include/boost/compute/algorithm/copy.hpp:222:1: note: template<class InputIterator, class OutputIterator> boost::compute::future<HostIterator> boost::compute::detail::dispatch_copy_async(InputIterator, InputIterator, OutputIterator, boost::compute::command_queue&, typename boost::enable_if_c<((boost::is_same<InputIterator, boost::compute::buffer_iterator<typename Iterator::value_type> >::value && boost::is_same<OutputIterator, boost::compute::buffer_iterator<typename OutputIterator::value_type> >::value) && boost::is_same<typename InputIterator::value_type, typename OutputIterator::value_type>::value)>::type*)  
 dispatch_copy_async(InputIterator first,  
 ^  
/home/fabian/code/compute/include/boost/compute/algorithm/copy.hpp:222:1: note:   template argument deduction/substitution failed:  
/home/fabian/code/compute/include/boost/compute/algorithm/copy.hpp: In substitution of ‘template<class InputIterator, class OutputIterator> boost::compute::future<HostIterator> boost::compute::detail::dispatch_copy_async(InputIterator, InputIterator, OutputIterator, boost::compute::command_queue&, typename boost::enable_if_c<((boost::is_same<InputIterator, boost::compute::buffer_iterator<typename Iterator::value_type> >::value && boost::is_same<OutputIterator, boost::compute::buffer_iterator<typename OutputIterator::value_type> >::value) && boost::is_same<typename InputIterator::value_type, typename OutputIterator::value_type>::value)>::type*) [with InputIterator = boost::compute::buffer_iterator<int>; OutputIterator = boost::compute::buffer_iterator<short int>]’:  
/home/fabian/code/compute/include/boost/compute/algorithm/copy.hpp:354:66:   required from ‘boost::compute::future<HostIterator> boost::compute::copy_async(InputIterator, InputIterator, OutputIterator, boost::compute::command_queue&) [with InputIterator = boost::compute::buffer_iterator<int>; OutputIterator = boost::compute::buffer_iterator<short int>]’  
/home/fabian/code/compute/test/test_copy.cpp:263:70:   required from here  
/home/fabian/code/compute/include/boost/compute/algorithm/copy.hpp:222:1: error: no type named ‘type’ in ‘struct boost::enable_if_c<false, void>’  
test/CMakeFiles/test_copy.dir/build.make:54: recipe for target 'test/CMakeFiles/test_copy.dir/test_copy.cpp.o' failed  
```

---

## Comment 1

> Username: kylelutz  
> Created at: 2014-11-26 04:01:47 UTC  
> Url: https://github.com/boostorg/compute/issues/313#issuecomment-64512113  

Interesting, I'll try to reproduce this locally.

---

## Comment 2

> Username: kylelutz  
> Created at: 2014-11-26 07:22:42 UTC  
> Url: https://github.com/boostorg/compute/issues/313#issuecomment-64524092  

Should be fixed in PR #314. Could you verify that on your machine?

---

## Comment 3

> Username: f-koehler  
> Created at: 2014-11-26 15:15:56 UTC  
> Url: https://github.com/boostorg/compute/issues/313#issuecomment-64659879  

Fine! Compiles and all tests succeed.
