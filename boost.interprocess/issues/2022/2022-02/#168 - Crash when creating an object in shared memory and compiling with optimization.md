# #168 - Crash when creating an object in shared memory and compiling with optimization [Closed]

> Username: mtg49  
> Created at: 2022-02-10 11:53:41 UTC  
> Updated at: 2026-01-07 09:43:19 UTC  
> Closed at: 2026-01-07 09:43:19 UTC  
> Url: https://github.com/boostorg/interprocess/issues/168  

A C++ application creating an object in shared memory crashes due to a segmentation violation when  
the application is compiled with -O2 or -O3. When compiling without the -O2 or -O3, the application works correctly.  
  
See below for the file tests.cc which is a part of a unit test using the Boost unit test framework.  
The crash occurs has been seen both when using Boost 1.65 and Boost 1.78.  
Could you please help me to find the reason for this crash?  
Please see various info related to this issue below.  
  
**Building the executable file**  
--------------------------------------------------------------------------------------------------------------------------------------------------------------------  
g++ -O3 -c tests.cc -o tests.o  
g++   tests.o -lboost_unit_test_framework -lpthread -lrt -o unit_tests  
  
  
**Output when crashing (compiled with -O2 or -O3)**  
---------------------------------------------------------------------------------------------------------------------------------------------------------------------  
./unit_tests   
Running 1 test case...  
unknown location(0): fatal error: in "test_1": signal: SIGSEGV, si_code: 128 (memory access violation at address: 0x00000000)  
tests.cc(66): last checkpoint: "test_1" test entry  
  
*** 1 failure is detected in the test module "shm_comm"  
  
  
**Output when compiling without -O2 and -O3**  
----------------------------------------------------------------------------------------------------------------------------------------------------------------------  
./unit_tests   
Running 1 test case...  
  
*** No errors detected  
  
**Compiler version**  
-------------------------------------------------------------------------------------------------------------------------------------------------------------------------  
gcc version 7.5.0 (Ubuntu 7.5.0-3ubuntu1~18.04)  
  
  
**File tests.cc**  
--------------------------------------------------------------------------------------------------------------------------------------------------------------------------  
#define BOOST_TEST_DYN_LINK  
#define BOOST_TEST_MAIN  
  
#define BOOST_TEST_MODULE shm_comm  
#include <boost/test/included/unit_test.hpp>  
  
#include <atomic>  
#include <boost/interprocess/managed_shared_memory.hpp>  
  
    
namespace bip = boost::interprocess;  
constexpr int CACHE_LINE_SIZE = 64;  
  
class S  
{  
   class AQC  
   {  
     public:  
       alignas(CACHE_LINE_SIZE) std::atomic<unsigned> head_ = {};  
   };  
  
   class AQ : public AQC  
   {  
     public:  
       alignas(CACHE_LINE_SIZE) std::atomic<uint64_t> elements_[3] = {};  
   };  
  
  public:  
    static S& getInstance()  
    {  
      static S   instance;  
      return instance;  
    }  
  
    bool f(void)  
    {  
      bip::shared_memory_object::remove("abc");  
  
      int number_of_bytes_in_shm = sizeof(AQ);  
  
      // Add space for other areas. This is not clear exactly what this is used for and its exact size.  
      number_of_bytes_in_shm += 600;  
        
      // Create a shared memory segment  
      mSegment = new bip::managed_shared_memory(bip::create_only, "abc", number_of_bytes_in_shm);  
  
      mAQ = mSegment->construct<AQ>("AQ")();  
      return true;  
    }  
  
    S(S const&)  = delete;  
    void operator=(S const&) = delete;  
  
  private:  
    S() {}   
    ~S() {}  
    bip::managed_shared_memory*   mSegment;  
    AQ*   mAQ;  
};  
  
  
BOOST_AUTO_TEST_CASE(test_1)  
{  
  S&  s = S::getInstance();  
  
  bool f = s.f();  
  BOOST_CHECK(f == true);  
}

---

## Comment 1

> Username: mtg49  
> Created at: 2022-02-10 11:56:38 UTC  
> Updated at: 2022-02-10 11:58:04 UTC  
> Url: https://github.com/boostorg/interprocess/issues/168#issuecomment-1034835519  

The stack shows the following when a crash has occurred:  
  
gdb ./unit_tests   
GNU gdb (Ubuntu 8.1-0ubuntu3.2) 8.1.0.20180409-git  
Copyright (C) 2018 Free Software Foundation, Inc.  
License GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>  
This is free software: you are free to change and redistribute it.  
There is NO WARRANTY, to the extent permitted by law.  Type "show copying"  
and "show warranty" for details.  
This GDB was configured as "x86_64-linux-gnu".  
Type "show configuration" for configuration details.  
For bug reporting instructions, please see:  
<http://www.gnu.org/software/gdb/bugs/>.  
Find the GDB manual and other documentation resources online at:  
<http://www.gnu.org/software/gdb/documentation/>.  
For help, type "help".  
Type "apropos word" to search for commands related to "word"...  
Reading symbols from ./unit_tests...(no debugging symbols found)...done.  
(gdb) r  
Starting program: unit_tests   
[Thread debugging using libthread_db enabled]  
Using host libthread_db library "/lib/x86_64-linux-gnu/libthread_db.so.1".  
Running 1 test case...  
  
Program received signal SIGSEGV, Segmentation fault.  
0x0000555555589a89 in boost::interprocess::ipcdetail::CtorArgN<S::AQ, false>::construct_n(void*, unsigned long, unsigned long&)  
    ()  
(gdb) info s  
#0  0x0000555555589a89 in boost::interprocess::ipcdetail::CtorArgN<S::AQ, false>::construct_n(void*, unsigned long, unsigned long&) ()  
#1  0x00005555555b7314 in void* boost::interprocess::segment_manager<char, boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family, boost::interprocess::offset_ptr<void, long, unsigned long, 0ul>, 0ul>, boost::interprocess::iset_index>::priv_generic_named_construct<char>(unsigned char, char const*, unsigned long, bool, bool, boost::interprocess::ipcdetail::in_place_interface&, boost::interprocess::iset_index<boost::interprocess::ipcdetail::index_config<char, boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family, boost::interprocess::offset_ptr<void, long, unsigned long, 0ul>, 0ul> > >&, boost::interprocess::ipcdetail::bool_<true>) ()  
#2  0x0000555555588369 in test_1::test_method() ()  
#3  0x0000555555588941 in test_1_invoker() ()  
#4  0x0000555555592c0e in boost::detail::function::function_obj_invoker0<boost::detail::forward, int>::invoke(boost::detail::function::function_buffer&) ()  
#5  0x000055555556c1f5 in boost::execution_monitor::catch_signals(boost::function<int ()> const&) ()  
#6  0x000055555556dff6 in boost::execution_monitor::execute(boost::function<int ()> const&) ()  
#7  0x000055555556e79d in boost::execution_monitor::vexecute(boost::function<void ()> const&) ()  
#8  0x0000555555573bc7 in boost::unit_test::unit_test_monitor_t::execute_and_translate(boost::function<void ()> const&, unsigned long) ()  
#9  0x00005555555a8c95 in boost::unit_test::framework::state::execute_test_tree(unsigned long, unsigned long, boost::unit_test::framework::state::random_generator_helper const*) ()  
#10 0x00005555555a8f13 in boost::unit_test::framework::state::execute_test_tree(unsigned long, unsigned long, boost::unit_test::framework::state::random_generator_helper const*) ()  
#11 0x000055555557bdbe in boost::unit_test::framework::run(unsigned long, bool) ()  
#12 0x0000555555587ddf in boost::unit_test::unit_test_main(boost::unit_test::test_suite* (*)(int, char**), int, char**) ()  
#13 0x00007ffff6c9dbf7 in __libc_start_main (main=0x5555555629d0 <main>, argc=1, argv=0x7fffffffdd68, init=<optimized out>,   
    fini=<optimized out>, rtld_fini=<optimized out>, stack_end=0x7fffffffdd58) at ../csu/libc-start.c:310  
#14 0x00005555555635ea in _start ()  
(gdb)

---

## Comment 2

> Username: mtg49  
> Created at: 2022-02-10 14:07:34 UTC  
> Url: https://github.com/boostorg/interprocess/issues/168#issuecomment-1034963284  

If the following line:  
alignas(CACHE_LINE_SIZE) std::atomic<uint64_t> elements_[3] = {};  
is changed so that the number of elements in elements_ is increased to 9 or more, there is no crash when  
compiling with -O2 or -O3.  
That is, for example:  
alignas(CACHE_LINE_SIZE) std::atomic<uint64_t> elements_[9] = {};  
given no crash when compiling with -O2 or -O3 given that all other things are unchanged.

---

## Comment 3

> Username: igaztanaga  
> Created at: 2026-01-07 09:43:19 UTC  
> Url: https://github.com/boostorg/interprocess/issues/168#issuecomment-3718055425  

This was caused by Boost.Interprocess not supporting overaligned types in "construct" calls. This overaligned construction feature was recently added in Boost 1.87.
