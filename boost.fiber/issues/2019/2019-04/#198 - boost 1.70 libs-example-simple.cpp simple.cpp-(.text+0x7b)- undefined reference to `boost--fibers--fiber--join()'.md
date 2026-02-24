# #198 - boost 1.70 libs/example/simple.cpp   simple.cpp:(.text+0x7b): undefined reference to `boost::fibers::fiber::join()' [Closed]

> Username: miniframework  
> Created at: 2019-04-17 08:10:10 UTC  
> Updated at: 2019-04-17 11:36:07 UTC  
> Closed at: 2019-04-17 08:40:56 UTC  
> Url: https://github.com/boostorg/fiber/issues/198  

~/opensoft/boost_1_70_0/output$ g++ -o simple simple.cpp  -I./include -lboost_context  -lboost_thread -L./lib  -std=c++11  
  
  
  
/tmp/ccsUlMZR.o: In function `main':  
simple.cpp:(.text+0x7b): undefined reference to `boost::fibers::fiber::join()'  
/tmp/ccsUlMZR.o: In function `boost::fibers::context::context(unsigned long, boost::fibers::type, boost::fibers::launch)':  
simple.cpp:(.text._ZN5boost6fibers7contextC2EmNS0_4typeENS0_6launchE[_ZN5boost6fibers7contextC5EmNS0_4typeENS0_6launchE]+0x18): undefined reference to `vtable for boost::fibers::context'  
/tmp/ccsUlMZR.o: In function `boost::fibers::fiber::get_id() const':  
simple.cpp:(.text._ZNK5boost6fibers5fiber6get_idEv[_ZNK5boost6fibers5fiber6get_idEv]+0x3b): undefined reference to `boost::fibers::context::get_id() const'  
/tmp/ccsUlMZR.o: In function `boost::this_fiber::yield()':  
simple.cpp:(.text._ZN5boost10this_fiber5yieldEv[_ZN5boost10this_fiber5yieldEv]+0x5): undefined reference to `boost::fibers::context::active()'  
simple.cpp:(.text._ZN5boost10this_fiber5yieldEv[_ZN5boost10this_fiber5yieldEv]+0xd): undefined reference to `boost::fibers::context::yield()'  
/tmp/ccsUlMZR.o: In function `boost::fibers::fiber::fiber<boost::context::basic_fixedsize_stack<boost::context::stack_traits>, void (&)(std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > const&, int), char const*, int>(boost::fibers::launch, std::allocator_arg_t, boost::context::basic_fixedsize_stack<boost::context::stack_traits>&&, void (&)(std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > const&, int), char const*, int)':  
simple.cpp:(.text._ZN5boost6fibers5fiberC2INS_7context21basic_fixedsize_stackINS3_12stack_traitsEEERFvRKNSt7__cxx1112basic_stringIcSt11char_traitsIcESaIcEEEiEJPKciEEENS0_6launchESt15allocator_arg_tOT_OT0_DpT1_[_ZN5boost6fibers5fiberC5INS_7context21basic_fixedsize_stackINS3_12stack_traitsEEERFvRKNSt7__cxx1112basic_stringIcSt11char_traitsIcESaIcEEEiEJPKciEEENS0_6launchESt15allocator_arg_tOT_OT0_DpT1_]+0x8f): undefined reference to `boost::fibers::fiber::start_()'  
/tmp/ccsUlMZR.o: In function `boost::fibers::worker_context<void (&)(std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > const&, int), char const*, int>::worker_context<boost::context::basic_fixedsize_stack<boost::context::stack_traits> >(boost::fibers::launch, boost::context::preallocated const&, boost::context::basic_fixedsize_stack<boost::context::stack_traits>&&, void (&)(std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > const&, int), char const*, int)':  
simple.cpp:(.text._ZN5boost6fibers14worker_contextIRFvRKNSt7__cxx1112basic_stringIcSt11char_traitsIcESaIcEEEiEJPKciEEC2INS_7context21basic_fixedsize_stackINSG_12stack_traitsEEEEENS0_6launchERKNSG_12preallocatedEOT_SB_SD_i[_ZN5boost6fibers14worker_contextIRFvRKNSt7__cxx1112basic_stringIcSt11char_traitsIcESaIcEEEiEJPKciEEC5INS_7context21basic_fixedsize_stackINSG_12stack_traitsEEEEENS0_6launchERKNSG_12preallocatedEOT_SB_SD_i]+0x168): undefined reference to `boost::fibers::context::~context()'  
/tmp/ccsUlMZR.o: In function `boost::fibers::worker_context<void (&)(std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > const&, int), char const*, int>::run_(boost::context::fiber&&)':  
simple.cpp:(.text._ZN5boost6fibers14worker_contextIRFvRKNSt7__cxx1112basic_stringIcSt11char_traitsIcESaIcEEEiEJPKciEE4run_EONS_7context5fiberE[_ZN5boost6fibers14worker_contextIRFvRKNSt7__cxx1112basic_stringIcSt11char_traitsIcESaIcEEEiEJPKciEE4run_EONS_7context5fiberE]+0x9e): undefined reference to `boost::fibers::context::terminate()'  
/tmp/ccsUlMZR.o: In function `boost::fibers::worker_context<void (&)(std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > const&, int), char const*, int>::~worker_context()':  
simple.cpp:(.text._ZN5boost6fibers14worker_contextIRFvRKNSt7__cxx1112basic_stringIcSt11char_traitsIcESaIcEEEiEJPKciEED2Ev[_ZN5boost6fibers14worker_contextIRFvRKNSt7__cxx1112basic_stringIcSt11char_traitsIcESaIcEEEiEJPKciEED5Ev]+0x20): undefined reference to `boost::fibers::context::~context()'  
/tmp/ccsUlMZR.o:(.rodata._ZTIN5boost6fibers14worker_contextIRFvRKNSt7__cxx1112basic_stringIcSt11char_traitsIcESaIcEEEiEJPKciEEE[_ZTIN5boost6fibers14worker_contextIRFvRKNSt7__cxx1112basic_stringIcSt11char_traitsIcESaIcEEEiEJPKciEEE]+0x10): undefined reference to `typeinfo for boost::fibers::context'  
collect2: error: ld returned 1 exit status

---

## Comment 1

> Username: olk  
> Created at: 2019-04-17 08:40:56 UTC  
> Url: https://github.com/boostorg/fiber/issues/198#issuecomment-483993180  

works for me - you do not link against boost.fiber ->  -lboost_fiber

---

## Comment 2

> Username: miniframework  
> Created at: 2019-04-17 09:15:26 UTC  
> Url: https://github.com/boostorg/fiber/issues/198#issuecomment-484004959  

> works for me - you do not link against boost.fiber -> -lboost_fiber  
  
lib not find libboost_fiber.so     1.70  only header ?

---

## Comment 3

> Username: olk  
> Created at: 2019-04-17 09:55:00 UTC  
> Url: https://github.com/boostorg/fiber/issues/198#issuecomment-484018144  

boost.fiber is not header-only - did you build with c++11

---

## Comment 4

> Username: miniframework  
> Created at: 2019-04-17 10:00:08 UTC  
> Url: https://github.com/boostorg/fiber/issues/198#issuecomment-484019802  

> boost.fiber is not header-only - did you build with c++11  
  
yes  build with c++11   cxxflags="-std=c++11"

---

## Comment 5

> Username: olk  
> Created at: 2019-04-17 11:36:07 UTC  
> Url: https://github.com/boostorg/fiber/issues/198#issuecomment-484046898  

I've no idea - I can find the libraries under <BOOST-DIR>/bin.v2/libs/context/build/gcc-8.2.1/
