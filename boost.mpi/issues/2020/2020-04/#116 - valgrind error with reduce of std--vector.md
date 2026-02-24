# #116 - valgrind error with reduce of std::vector [Closed]

> Username: francescopt  
> Created at: 2020-04-25 16:05:47 UTC  
> Updated at: 2020-06-19 12:10:21 UTC  
> Closed at: 2020-06-19 12:10:20 UTC  
> Url: https://github.com/boostorg/mpi/issues/116  

Running the following program with valgrind  
  
```  
#include <iostream>  
#include <vector>  
#include <boost/mpi/environment.hpp>  
#include <boost/mpi/communicator.hpp>  
#include <boost/mpi/collectives.hpp>  
#include <boost/mpi/operations.hpp>  
#include <boost/serialization/vector.hpp>  
  
struct sum_vec {  
  std::vector<int> operator()(const std::vector<int>& a, const std::vector<int>& b) const {  
    std::vector<int> res(a.size());  
    std::transform(a.begin(), a.end(), b.begin(), res.begin(), std::plus<int>{});  
    return res;  
  }  
};  
  
int main()  
{  
  boost::mpi::environment env;  
  boost::mpi::communicator world;  
  
  std::vector<std::vector<int>> v = { std::vector<int>{ world.rank() + 1, 2 * (world.rank() + 1) },  
				      std::vector<int>{ 3 * (world.rank() + 1), 4 * (world.rank() + 1) }  } ;  
  if (world.rank() == 0)  
    {  
      std::vector<std::vector<int>> v_reduced(v.size());  
      boost::mpi::reduce(world, &v.front(), v.size(), &v_reduced.front(), sum_vec{}, 0);  
      std::cout << "reduced vector:" << std::endl;  
      for (auto const& el : v_reduced)  
	{  
	  for (auto const& i : el)  
	    std::cout << i << ' ';  
	  std::cout << std::endl;  
	}  
    }  
  else  
    {  
      boost::mpi::reduce(world, &v.front(), v.size(), sum_vec{}, 0);  
    }  
  
  return 0;  
}  
  
```  
  
gives the error  
  
==408229== Thread 1:  
==408229== Uninitialised byte(s) found during client check request  
==408229==    at 0x485221E: check_mem_is_defined_untyped (libmpiwrap.c:953)  
==408229==    by 0x485221E: PMPI_Get_count (libmpiwrap.c:1132)  
==408229==    by 0x4915A97: boost::mpi::detail::packed_archive_recv(boost::mpi::communicator const&, int, int, boost::mpi::packed_iarchive&, ompi_status_public_t&) (in /usr/lib/libboost_mpi.so.1.72.0)  
==408229==    by 0x12149E: void boost::mpi::detail::tree_reduce_impl<std::vector<int, std::allocator<int> >, sum_vec>(boost::mpi::communicator const&, std::vector<int, std::allocator<int> > const*, int, std::vector<int, std::allocator<int> >*, sum_vec, int, mpl_::bool_<false>) (in /tmp/reduce_vec)  
==408229==    by 0x120142: void boost::mpi::detail::reduce_impl<std::vector<int, std::allocator<int> >, sum_vec>(boost::mpi::communicator const&, std::vector<int, std::allocator<int> > const*, int, std::vector<int, std::allocator<int> >*, sum_vec, int, mpl_::bool_<false>, mpl_::bool_<false>) (in /tmp/reduce_vec)  
==408229==    by 0x11EDC1: void boost::mpi::reduce<std::vector<int, std::allocator<int> >, sum_vec>(boost::mpi::communicator const&, std::vector<int, std::allocator<int> > const*, int, std::vector<int, std::allocator<int> >*, sum_vec, int) (in /tmp/reduce_vec)  
==408229==    by 0x118220: main (in /tmp/reduce_vec)  
==408229==  Address 0x1ffefff3f8 is on thread 1's stack  
==408229==  in frame #2, created by void boost::mpi::detail::tree_reduce_impl<std::vector<int, std::allocator<int> >, sum_vec>(boost::mpi::communicator const&, std::vector<int, std::allocator<int> > const*, int, std::vector<int, std::allocator<int> >*, sum_vec, int, mpl_::bool_<false>) (???:)  
==408229==   
  
The error persists if, say, ```v_reduced``` is fully initialized as  
  
      std::vector<std::vector<int>> v_reduced(v.size(), std::vector<int>(2, 1));  
  
The error remains also when using a C-style array of ```std::vector<int>``` instead of a vector of a vector:  
  
```  
....  
      std::vector<int>* v_reduced = new std::vector<int>[v.size()];  
      boost::mpi::reduce(world, &v.front(), v.size(), v_reduced, sum_vec{}, 0);  
      std::cout << "reduced vector:" << std::endl;  
      for (std::size_t i = 0; i < v.size(); ++i)  
	{  
	  for (auto const& i : v_reduced[i])  
	    std::cout << i << ' ';  
	  std::cout << std::endl;  
	}  
      delete[] v_reduced;  
...  
```  
  
I am using  
-gcc 9.3.0  
-valgrind 3.15.0  
-boost 1.72.0  
-openmpi 4.0.3 (compiled with support for MPI1)

---

## Comment 1

> Username: aminiussi  
> Created at: 2020-06-19 12:09:52 UTC  
> Url: https://github.com/boostorg/mpi/issues/116#issuecomment-646601227  

Hi, these seems to be MPI specific issues, As they appear in an MPI specific implementation code.  
  
Although I cannot be sure as it's just one call after Boost.MPI code.  
  
Right now, I cannot reproduce with the current develop, OpenMPI 4.0.4 and g++ 7.5.0  
  
So I'm going to close but please re-open if issues is still in next release.

---

## Comment 2

> Username: aminiussi  
> Created at: 2020-06-19 12:10:20 UTC  
> Url: https://github.com/boostorg/mpi/issues/116#issuecomment-646601425  

Here is my trace:  
```  
alainm@jarvis:~/views/boostorg/boost/libs/mpi/test$ mpirun -n 2 valgrind ../../../bin.v2/libs/mpi/test/vector_sum-2.test/gcc-7/debug/threading-multi/visibility-hidden/vector_sum-2   
==17628== Memcheck, a memory error detector  
==17628== Copyright (C) 2002-2017, and GNU GPL'd, by Julian Seward et al.  
==17628== Using Valgrind-3.13.0 and LibVEX; rerun with -h for copyright info  
==17628== Command: ../../../bin.v2/libs/mpi/test/vector_sum-2.test/gcc-7/debug/threading-multi/visibility-hidden/vector_sum-2  
==17628==   
==17629== Memcheck, a memory error detector  
==17629== Copyright (C) 2002-2017, and GNU GPL'd, by Julian Seward et al.  
==17629== Using Valgrind-3.13.0 and LibVEX; rerun with -h for copyright info  
==17629== Command: ../../../bin.v2/libs/mpi/test/vector_sum-2.test/gcc-7/debug/threading-multi/visibility-hidden/vector_sum-2  
==17629==   
==17628== Thread 3:  
==17628== Syscall param writev(vector[...]) points to uninitialised byte(s)  
==17628==    at 0x5C8F6E7: writev (writev.c:26)  
==17628==    by 0x927180C: OPAL_MCA_PMIX3X_pmix_ptl_base_send_handler (in /opt/openmpi-4.0.4/lib/openmpi/mca_pmix_pmix3x.so)  
==17628==    by 0x64C5A30: event_persist_closure (event.c:1321)  
==17628==    by 0x64C5A30: event_process_active_single_queue (event.c:1365)  
==17628==    by 0x64C5A30: event_process_active (event.c:1440)  
==17628==    by 0x64C5A30: opal_libevent2022_event_base_loop (event.c:1644)  
==17628==    by 0x921665D: progress_engine (in /opt/openmpi-4.0.4/lib/openmpi/mca_pmix_pmix3x.so)  
==17628==    by 0x5F716DA: start_thread (pthread_create.c:463)  
==17628==    by 0x5C9A88E: clone (clone.S:95)  
==17628==  Address 0xd8be847 is 71 bytes inside a block of size 5,120 alloc'd  
==17628==    at 0x4C31D2F: realloc (in /usr/lib/valgrind/vgpreload_memcheck-amd64-linux.so)  
==17628==    by 0x925113A: OPAL_MCA_PMIX3X_pmix_bfrop_buffer_extend (in /opt/openmpi-4.0.4/lib/openmpi/mca_pmix_pmix3x.so)  
==17628==    by 0x92542B7: OPAL_MCA_PMIX3X_pmix_bfrops_base_pack_byte (in /opt/openmpi-4.0.4/lib/openmpi/mca_pmix_pmix3x.so)  
==17628==    by 0x9254BCD: OPAL_MCA_PMIX3X_pmix_bfrops_base_pack_buf (in /opt/openmpi-4.0.4/lib/openmpi/mca_pmix_pmix3x.so)  
==17628==    by 0x92544BA: OPAL_MCA_PMIX3X_pmix_bfrops_base_pack (in /opt/openmpi-4.0.4/lib/openmpi/mca_pmix_pmix3x.so)  
==17628==    by 0x91C9743: _commitfn (in /opt/openmpi-4.0.4/lib/openmpi/mca_pmix_pmix3x.so)  
==17628==    by 0x64C6008: event_process_active_single_queue (event.c:1370)  
==17628==    by 0x64C6008: event_process_active (event.c:1440)  
==17628==    by 0x64C6008: opal_libevent2022_event_base_loop (event.c:1644)  
==17628==    by 0x921665D: progress_engine (in /opt/openmpi-4.0.4/lib/openmpi/mca_pmix_pmix3x.so)  
==17628==    by 0x5F716DA: start_thread (pthread_create.c:463)  
==17628==    by 0x5C9A88E: clone (clone.S:95)  
==17628==   
==17629== Thread 3:  
==17629== Syscall param writev(vector[...]) points to uninitialised byte(s)  
==17629==    at 0x5C8F6E7: writev (writev.c:26)  
==17629==    by 0x927180C: OPAL_MCA_PMIX3X_pmix_ptl_base_send_handler (in /opt/openmpi-4.0.4/lib/openmpi/mca_pmix_pmix3x.so)  
==17629==    by 0x64C5A30: event_persist_closure (event.c:1321)  
==17629==    by 0x64C5A30: event_process_active_single_queue (event.c:1365)  
==17629==    by 0x64C5A30: event_process_active (event.c:1440)  
==17629==    by 0x64C5A30: opal_libevent2022_event_base_loop (event.c:1644)  
==17629==    by 0x921665D: progress_engine (in /opt/openmpi-4.0.4/lib/openmpi/mca_pmix_pmix3x.so)  
==17629==    by 0x5F716DA: start_thread (pthread_create.c:463)  
==17629==    by 0x5C9A88E: clone (clone.S:95)  
==17629==  Address 0xd8be477 is 71 bytes inside a block of size 5,120 alloc'd  
==17629==    at 0x4C31D2F: realloc (in /usr/lib/valgrind/vgpreload_memcheck-amd64-linux.so)  
==17629==    by 0x925113A: OPAL_MCA_PMIX3X_pmix_bfrop_buffer_extend (in /opt/openmpi-4.0.4/lib/openmpi/mca_pmix_pmix3x.so)  
==17629==    by 0x92542B7: OPAL_MCA_PMIX3X_pmix_bfrops_base_pack_byte (in /opt/openmpi-4.0.4/lib/openmpi/mca_pmix_pmix3x.so)  
==17629==    by 0x9254BCD: OPAL_MCA_PMIX3X_pmix_bfrops_base_pack_buf (in /opt/openmpi-4.0.4/lib/openmpi/mca_pmix_pmix3x.so)  
==17629==    by 0x92544BA: OPAL_MCA_PMIX3X_pmix_bfrops_base_pack (in /opt/openmpi-4.0.4/lib/openmpi/mca_pmix_pmix3x.so)  
==17629==    by 0x91C9743: _commitfn (in /opt/openmpi-4.0.4/lib/openmpi/mca_pmix_pmix3x.so)  
==17629==    by 0x64C6008: event_process_active_single_queue (event.c:1370)  
==17629==    by 0x64C6008: event_process_active (event.c:1440)  
==17629==    by 0x64C6008: opal_libevent2022_event_base_loop (event.c:1644)  
==17629==    by 0x921665D: progress_engine (in /opt/openmpi-4.0.4/lib/openmpi/mca_pmix_pmix3x.so)  
==17629==    by 0x5F716DA: start_thread (pthread_create.c:463)  
==17629==    by 0x5C9A88E: clone (clone.S:95)  
==17629==   
reduced vector:  
3 6   
9 12   
==17629==   
==17629== HEAP SUMMARY:  
==17629==     in use at exit: 4,905 bytes in 26 blocks  
==17629==   total heap usage: 21,693 allocs, 21,667 frees, 4,312,947 bytes allocated  
==17629==   
==17629== LEAK SUMMARY:  
==17629==    definitely lost: 449 bytes in 14 blocks  
==17629==    indirectly lost: 4,281 bytes in 7 blocks  
==17629==      possibly lost: 0 bytes in 0 blocks  
==17629==    still reachable: 175 bytes in 5 blocks  
==17629==         suppressed: 0 bytes in 0 blocks  
==17629== Rerun with --leak-check=full to see details of leaked memory  
==17629==   
==17629== For counts of detected and suppressed errors, rerun with: -v  
==17629== Use --track-origins=yes to see where uninitialised values come from  
==17629== ERROR SUMMARY: 1 errors from 1 contexts (suppressed: 0 from 0)  
==17628==   
==17628== HEAP SUMMARY:  
==17628==     in use at exit: 4,905 bytes in 26 blocks  
==17628==   total heap usage: 21,707 allocs, 21,681 frees, 4,314,194 bytes allocated  
==17628==   
==17628== LEAK SUMMARY:  
==17628==    definitely lost: 449 bytes in 14 blocks  
==17628==    indirectly lost: 4,281 bytes in 7 blocks  
==17628==      possibly lost: 0 bytes in 0 blocks  
==17628==    still reachable: 175 bytes in 5 blocks  
==17628==         suppressed: 0 bytes in 0 blocks  
==17628== Rerun with --leak-check=full to see details of leaked memory  
==17628==   
==17628== For counts of detected and suppressed errors, rerun with: -v  
==17628== Use --track-origins=yes to see where uninitialised values come from  
==17628== ERROR SUMMARY: 1 errors from 1 contexts (suppressed: 0 from 0)  
alainm@jarvis:~/views/boostorg/boost/libs/mpi/test$   
```
