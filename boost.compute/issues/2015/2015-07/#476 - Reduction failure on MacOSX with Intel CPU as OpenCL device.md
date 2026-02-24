# #476 - Reduction failure on MacOSX with Intel CPU as OpenCL device [Closed]

> Username: fpvidal  
> Created at: 2015-07-10 09:04:30 UTC  
> Updated at: 2015-08-13 17:23:45 UTC  
> Closed at: 2015-08-13 17:23:41 UTC  
> Url: https://github.com/boostorg/compute/issues/476  

With Intel CPU devices on Mac OS X: Doesn't properly support OpenCL __local memory when the work-group size is greater than one.  
  
Below are the results of test_reduce:  
  
 /usr/local/share/compute/bin/test->./test_reduce  
Running 12 test cases...  
/usr/local/share/compute/include/boost/compute/command_queue.hpp:1040: fatal error in "boost::compute::event boost::compute::command_queue::enqueue_nd_range_kernel(const boost::compute::kernel &, size_t, const size_t *, const size_t *, const size_t *, const boost::compute::wait_list &)": std::exception: Invalid Work Group Size  
/usr/local/share/compute/include/boost/compute/command_queue.hpp:1040: fatal error in "boost::compute::event boost::compute::command_queue::enqueue_nd_range_kernel(const boost::compute::kernel &, size_t, const size_t *, const size_t *, const size_t *, const boost::compute::wait_list &)": std::exception: Invalid Work Group Size  
/usr/local/share/compute/test/test_reduce.cpp:48: last checkpoint  
-- Repeat-fill case  
/usr/local/share/compute/include/boost/compute/command_queue.hpp:1040: fatal error in "boost::compute::event boost::compute::command_queue::enqueue_nd_range_kernel(const boost::compute::kernel &, size_t, const size_t *, const size_t *, const size_t *, const boost::compute::wait_list &)": std::exception: Invalid Work Group Size  
/usr/local/share/compute/test/test_reduce.cpp:48: last checkpoint  
/usr/local/share/compute/include/boost/compute/command_queue.hpp:1040: fatal error in "boost::compute::event boost::compute::command_queue::enqueue_nd_range_kernel(const boost::compute::kernel &, size_t, const size_t *, const size_t *, const size_t *, const boost::compute::wait_list &)": std::exception: Invalid Work Group Size  
/usr/local/share/compute/test/test_reduce.cpp:48: last checkpoint  
/usr/local/share/compute/include/boost/compute/command_queue.hpp:1040: fatal error in "boost::compute::event boost::compute::command_queue::enqueue_nd_range_kernel(const boost::compute::kernel &, size_t, const size_t *, const size_t *, const size_t *, const boost::compute::wait_list &)": std::exception: Invalid Work Group Size  
/usr/local/share/compute/test/test_reduce.cpp:112: last checkpoint  
/usr/local/share/compute/include/boost/compute/command_queue.hpp:1040: fatal error in "boost::compute::event boost::compute::command_queue::enqueue_nd_range_kernel(const boost::compute::kernel &, size_t, const size_t *, const size_t *, const size_t *, const boost::compute::wait_list &)": std::exception: Invalid Work Group Size  
/usr/local/share/compute/test/test_reduce.cpp:112: last checkpoint  
/usr/local/share/compute/include/boost/compute/command_queue.hpp:1040: fatal error in "boost::compute::event boost::compute::command_queue::enqueue_nd_range_kernel(const boost::compute::kernel &, size_t, const size_t *, const size_t *, const size_t *, const boost::compute::wait_list &)": std::exception: Invalid Work Group Size  
/usr/local/share/compute/test/test_reduce.cpp:112: last checkpoint  
/usr/local/share/compute/include/boost/compute/command_queue.hpp:1040: fatal error in "boost::compute::event boost::compute::command_queue::enqueue_nd_range_kernel(const boost::compute::kernel &, size_t, const size_t *, const size_t *, const size_t *, const boost::compute::wait_list &)": std::exception: Invalid Work Group Size  
/usr/local/share/compute/test/test_reduce.cpp:112: last checkpoint  
/usr/local/share/compute/include/boost/compute/command_queue.hpp:1040: fatal error in "boost::compute::event boost::compute::command_queue::enqueue_nd_range_kernel(const boost::compute::kernel &, size_t, const size_t *, const size_t *, const size_t *, const boost::compute::wait_list &)": std::exception: Invalid Work Group Size  
/usr/local/share/compute/test/test_reduce.cpp:112: last checkpoint  
/usr/local/share/compute/include/boost/compute/command_queue.hpp:1040: fatal error in "boost::compute::event boost::compute::command_queue::enqueue_nd_range_kernel(const boost::compute::kernel &, size_t, const size_t *, const size_t *, const size_t *, const boost::compute::wait_list &)": std::exception: Invalid Work Group Size  
/usr/local/share/compute/test/test_reduce.cpp:112: last checkpoint  
  
**\* 10 failures detected in test suite "TestReduce"  
  
NB: Issue opened to make sure we don't lose track of the problem

---

## Comment 1

> Username: jszuppe  
> Created at: 2015-07-10 11:45:32 UTC  
> Url: https://github.com/boostorg/compute/issues/476#issuecomment-120393369  

I think it's known bug in Mac OS OpenCL drivers. I know a few algorithms need special implementation for CPU without usage of local memory because of this bug.   
However, in this case `serial_reduce` should be used to perform reduction and it does not use local memory, so everything should be fine. I don't have Mac OS X, so I can't reproduce the problem.

---

## Comment 2

> Username: jszuppe  
> Created at: 2015-08-08 17:05:59 UTC  
> Url: https://github.com/boostorg/compute/issues/476#issuecomment-129013009  

I think I located the bug. https://github.com/boostorg/compute/pull/491 should fix it.  Try `develop` branch after it's merged.

---

## Comment 3

> Username: kylelutz  
> Created at: 2015-08-13 17:23:41 UTC  
> Url: https://github.com/boostorg/compute/issues/476#issuecomment-130769174  

This should now be fixed in `develop`. Please re-open if you still encounter errors. Thanks!
