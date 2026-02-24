# #371 - Memory leak issue in reset() function from boost::thread_specific_ptr [Open]

> Username: HareeshAgraharam  
> Created at: 2022-05-16 06:37:05 UTC  
> Updated at: 2022-05-16 08:26:56 UTC  
> Url: https://github.com/boostorg/thread/issues/371  

Following memory leak issue is observed with reset() method from boost::thread_specific_ptr by using valgrind tool in Boost 1.75.  
  
  
**### Leak description:**  
```  
==27891== 24 bytes in 1 blocks are still reachable in loss record 1 of 2  
==27891==    at 0x4835DEF: operator new(unsigned long) (in /usr/lib/x86_64-linux-gnu/valgrind/vgpreload_memcheck-amd64-linux.so)  
==27891==    by 0x113064: boost::detail::make_external_thread_data() (in /home/{user}/boost_1_75_0/a.out)  
==27891==    by 0x114084: boost::detail::add_new_tss_node(void const*, void (*)(void (*)(void*), void*), void (*)(void*), void*) (in /home/{user}/boost_1_75_0/a.out)  
==27891==    by 0x1102F5: boost::thread_specific_ptr<int>::reset(int*) (in /home/{user}/boost_1_75_0/a.out)  
==27891==    by 0x10DC07: main (in /home/{user}/boost_1_75_0/a.out)  
  
```  
  
Code Snippet:  
```  
#include <boost/thread.hpp>  
#include <iostream>  
int main()  
{  
	boost::thread_specific_ptr<int> conv_thread_ptr;  
	if(!conv_thread_ptr.get())  
	{  
        	conv_thread_ptr.reset(new int);  
	}  
	return 0;  
}  
```  
  
Command used for compilation:   
`g++ main.cpp -I./boost_1_75_0_install/include ./boost_1_75_0_install/lib/libboost_thread.a -lpthread`  
  
Command used for memory leak checking with valgrind:  
`valgrind --leak-check=full --show-leak-kinds=all --track-origins=yes --verbose --log-file=valgrind-out.txt ./a.out`  
  
  
  
@vaishnavkatiyar
