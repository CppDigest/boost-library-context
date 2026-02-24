# #370 - limit_handles object not thread-safe [Open]

> Username: dkl  
> Created at: 2024-05-13 18:25:23 UTC  
> Updated at: 2024-05-13 18:26:02 UTC  
> Url: https://github.com/boostorg/process/issues/370  

Hi,  
  
I've tried using `boost::process::limit_handles` from multiple threads in parallel, but it doesn't seem to be supported at the moment. `boost::process::limit_handles` is an instance of `struct limit_handles_`, which contains mutable data members. In case of POSIX there is a `mutable std::vector<int> used_handles;`. But there is no mutex lock, which seems to be causing crashes sometimes like segfault, double free, etc. Thread sanitizer reports data races on the vector.  
  
I didn't test the Windows implementation but noticed that it also has a mutable data member, so it may be affected by the same issue.  
  
As a work-around, would it be safe to pass a locally instantiated `struct limit_handles_` object instead of the global `limit_handles`?  
  
Code example:  
```c++  
#include <stdio.h>  
#include <thread>  
#include <boost/process.hpp>  
  
//#define WORKAROUND  
  
static void run_it()  
{  
	for (size_t i = 0; i < 100000; ++i) {  
#ifdef WORKAROUND  
		typeof(boost::process::limit_handles) limit_handles;  
#endif  
  
		boost::asio::io_context ioctx;  
		boost::process::child child{  
			ioctx,  
			boost::process::argv({"/usr/bin/true"}),  
			boost::process::std_in < stdin,  
			boost::process::std_out > stdout,  
			boost::process::std_err > stderr,  
#ifdef WORKAROUND  
			limit_handles,  
#else  
			boost::process::limit_handles,  
#endif  
		};  
  
		ioctx.run();  
		child.wait();  
		if (child.exit_code() != 0) {  
			printf("%i: child exited with %i\n", gettid(), child.exit_code());  
		}  
	}  
}  
  
int main()  
{  
	std::thread thread([]() { run_it(); });  
	run_it();  
	thread.join();  
	return 0;  
}  
```  
  
Example observations (unexpected malloc/free error aborts, unexpected segfaults):  
```  
~$ g++ -Wall -O2 -g a.cxx -o a   
~$ time ./a  
free(): unaligned chunk detected in tcache 2  
Aborted (core dumped)  
  
real    0m9,641s  
user    0m14,931s  
sys     0m5,154s  
~$ time ./a  
free(): unaligned chunk detected in tcache 2  
Aborted (core dumped)  
  
real    0m1,050s  
user    0m1,565s  
sys     0m0,526s  
~$ time ./a  
Segmentation fault (core dumped)  
  
real    0m3,439s  
user    0m5,185s  
sys     0m1,883s  
~$ time ./a  
free(): unaligned chunk detected in tcache 2  
Aborted (core dumped)  
  
real    0m0,910s  
user    0m1,304s  
sys     0m0,460s  
~$ time ./a  
Segmentation fault (core dumped)  
  
real    0m1,863s  
user    0m2,802s  
sys     0m0,979s  
~$ time ./a  
free(): unaligned chunk detected in tcache 2  
Aborted (core dumped)  
  
real    0m3,612s  
user    0m5,499s  
sys     0m1,881s  
```  
  
Various TSan errors can be observed when compiling with `-fsanitize=thread`, for example (summarized output...):  
```  
SUMMARY: ThreadSanitizer: data race /usr/include/c++/11/bits/stl_vector.h:111 in std::_Vector_base<int, std::allocator<int> >::_Vector_impl_data::_M_copy_data(std::_Vector_base<int, std::allocator<int> >::_Vector_impl_data const&)  
SUMMARY: ThreadSanitizer: data race /usr/include/c++/11/bits/stl_vector.h:113 in std::_Vector_base<int, std::allocator<int> >::_Vector_impl_data::_M_copy_data(std::_Vector_base<int, std::allocator<int> >::_Vector_impl_data const&)  
SUMMARY: ThreadSanitizer: data race /usr/include/c++/11/bits/stl_vector.h:112 in std::_Vector_base<int, std::allocator<int> >::_Vector_impl_data::_M_copy_data(std::_Vector_base<int, std::allocator<int> >::_Vector_impl_data const&)  
SUMMARY: ThreadSanitizer: data race ../../../../src/libsanitizer/tsan/tsan_new_delete.cpp:150 in operator delete(void*, unsigned long)  
SUMMARY: ThreadSanitizer: data race ../../../../src/libsanitizer/tsan/tsan_new_delete.cpp:150 in operator delete(void*, unsigned long)  
SUMMARY: ThreadSanitizer: SEGV /usr/local/include/boost/asio/detail/impl/service_registry.ipp:85 in boost::asio::detail::service_registry::notify_fork(boost::asio::execution_context::fork_event)  
```
