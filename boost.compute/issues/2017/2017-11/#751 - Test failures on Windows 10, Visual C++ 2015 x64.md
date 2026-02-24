# #751 - Test failures on Windows 10, Visual C++ 2015 x64 [Closed]

> Username: Kristian-Popov  
> Created at: 2017-11-12 17:14:28 UTC  
> Updated at: 2017-11-17 07:59:24 UTC  
> Closed at: 2017-11-17 07:59:24 UTC  
> Url: https://github.com/boostorg/compute/issues/751  

Good evening!  
Just found that some tests fail to compile and some fail (but compile without problems) on my system.  
### System/build information  
OS: Windows 10.  
Compiler: Visual C++ 2015,  
Build: x64.  
Commit: latest commit from master branch at a moment of writing (hash d36ef02b7860b9bcf5ea49)  
System configuration: Core i7 7700HQ, GPU Geforce 1050 Ti. Geforce GPU is used as a default device.  
Feel free to contact me if more information is needed.  
  
### Test failures  
Only container.vector tests failed, here's the output:  
`        Start  97: container.vector  
  
97: Test command: C:\Users\chris-pet-projects\Documents\boost-compute-build\test\Release\test_vector.exe  
97: Test timeout computed to be: 9.99988e+006  
97: Running 27 test cases...  
97: C:/Users/chris-pet-projects/Documents/boost-compute/test/test_vector.cpp(75): error: in "compute_test/reserve": check int_vector.capacity() == size_t(20 * growth_factor) has failed [10 != 30]  
97: C:/Users/chris-pet-projects/Documents/boost-compute/test/test_vector.cpp(79): error: in "compute_test/reserve": check int_vector.capacity() == size_t(20 * growth_factor) has failed [10 != 30]  
97: C:/Users/chris-pet-projects/Documents/boost-compute/test/test_vector.cpp(483): error: in "compute_test/shrink_to_fit": check int_vector.capacity() >= 15 has failed  
97: C:/Users/chris-pet-projects/Documents/boost-compute/test/test_vector.cpp(495): error: in "compute_test/shrink_to_fit": check int_vector.capacity() == 0 has failed [5 != 0]  
97: C:/Users/chris-pet-projects/Documents/boost-compute/test/test_vector.cpp(499): error: in "compute_test/shrink_to_fit": check int_vector.capacity() >= 15 has failed  
97:   
97: *** 5 failures are detected in the test module "TestVector"  
 97/147 Test  #97: container.vector .....................***Failed    0.73 sec `  
  
### Compilation errors:  
  
2>C:\Users\chris-pet-projects\Documents\boost-compute\test\test_device.cpp(240): error C2039: 'get_host_timer': is not a member of 'boost::compute::device'  
2>  C:\Users\chris-pet-projects\Documents\boost_1_65_1\boost/compute/device.hpp(46): note: see declaration of 'boost::compute::device'  
2>C:\Users\chris-pet-projects\Documents\boost-compute\test\test_device.cpp(244): error C2039: 'get_host_timer': is not a member of 'boost::compute::device'  
2>  C:\Users\chris-pet-projects\Documents\boost_1_65_1\boost/compute/device.hpp(46): note: see declaration of 'boost::compute::device'  
2>C:\Users\chris-pet-projects\Documents\boost-compute\test\test_device.cpp(244): error C2059: syntax error: ')'  
2>C:\Users\chris-pet-projects\Documents\boost-compute\test\test_device.cpp(249): error C2039: 'get_host_timer': is not a member of 'boost::compute::device'  
2>  C:\Users\chris-pet-projects\Documents\boost_1_65_1\boost/compute/device.hpp(46): note: see declaration of 'boost::compute::device'  
2>C:\Users\chris-pet-projects\Documents\boost-compute\test\test_device.cpp(249): error C2059: syntax error: ')'  
2>C:\Users\chris-pet-projects\Documents\boost-compute\test\test_device.cpp(258): error C2039: 'get_device_and_host_timer': is not a member of 'boost::compute::device'  
2>  C:\Users\chris-pet-projects\Documents\boost_1_65_1\boost/compute/device.hpp(46): note: see declaration of 'boost::compute::device'  
2>C:\Users\chris-pet-projects\Documents\boost-compute\test\test_device.cpp(264): error C2039: 'get_device_and_host_timer': is not a member of 'boost::compute::device'  
2>  C:\Users\chris-pet-projects\Documents\boost_1_65_1\boost/compute/device.hpp(46): note: see declaration of 'boost::compute::device'  
2>C:\Users\chris-pet-projects\Documents\boost-compute\test\test_device.cpp(264): error C2059: syntax error: ')'  
2>C:\Users\chris-pet-projects\Documents\boost-compute\test\test_device.cpp(265): error C2039: 'get_device_and_host_timer': is not a member of 'boost::compute::device'  
2>  C:\Users\chris-pet-projects\Documents\boost_1_65_1\boost/compute/device.hpp(46): note: see declaration of 'boost::compute::device'  
2>C:\Users\chris-pet-projects\Documents\boost-compute\test\test_device.cpp(265): error C2059: syntax error: ')'  
2>C:\Users\chris-pet-projects\Documents\boost-compute\test\test_device.cpp(270): error C2039: 'get_device_and_host_timer': is not a member of 'boost::compute::device'  
2>  C:\Users\chris-pet-projects\Documents\boost_1_65_1\boost/compute/device.hpp(46): note: see declaration of 'boost::compute::device'  
2>C:\Users\chris-pet-projects\Documents\boost-compute\test\test_device.cpp(270): error C2059: syntax error: ')'  
2>C:\Users\chris-pet-projects\Documents\boost-compute\test\test_device.cpp(271): error C2039: 'get_device_and_host_timer': is not a member of 'boost::compute::device'  
2>  C:\Users\chris-pet-projects\Documents\boost_1_65_1\boost/compute/device.hpp(46): note: see declaration of 'boost::compute::device'  
2>C:\Users\chris-pet-projects\Documents\boost-compute\test\test_device.cpp(271): error C2059: syntax error: ')'  
2>C:\Users\chris-pet-projects\Documents\boost-compute\test\test_device.cpp(279): error C2672: 'boost::compute::device::get_info': no matching overloaded function found  
2>C:\Users\chris-pet-projects\Documents\boost-compute\test\test_device.cpp(279): error C2770: invalid explicit template argument(s) for 'detail::get_object_info_type<boost::compute::device,Enum>::type boost::compute::device::get_info(void) const'  
2>  C:\Users\chris-pet-projects\Documents\boost_1_65_1\boost/compute/device.hpp(322): note: see declaration of 'boost::compute::device::get_info'  
2>C:\Users\chris-pet-projects\Documents\boost-compute\test\test_device.cpp(279): error C2974: 'boost::compute::device::get_info': invalid template argument for 'T', type expected  
2>  C:\Users\chris-pet-projects\Documents\boost_1_65_1\boost/compute/device.hpp(314): note: see declaration of 'boost::compute::device::get_info'  
2>C:\Users\chris-pet-projects\Documents\boost-compute\test\test_device.cpp(279): error C2228: left of '.empty' must have class/struct/union  
2>C:\Users\chris-pet-projects\Documents\boost-compute\test\test_device.cpp(280): error C2672: 'boost::compute::device::get_info': no matching overloaded function found  
2>C:\Users\chris-pet-projects\Documents\boost-compute\test\test_device.cpp(280): error C2770: invalid explicit template argument(s) for 'detail::get_object_info_type<boost::compute::device,Enum>::type boost::compute::device::get_info(void) const'  
2>  C:\Users\chris-pet-projects\Documents\boost_1_65_1\boost/compute/device.hpp(322): note: see declaration of 'boost::compute::device::get_info'  
2>C:\Users\chris-pet-projects\Documents\boost-compute\test\test_device.cpp(280): error C2974: 'boost::compute::device::get_info': invalid template argument for 'T', type expected  
2>  C:\Users\chris-pet-projects\Documents\boost_1_65_1\boost/compute/device.hpp(314): note: see declaration of 'boost::compute::device::get_info'  
2>C:\Users\chris-pet-projects\Documents\boost-compute\test\test_device.cpp(283): error C2672: 'boost::compute::device::get_info': no matching overloaded function found  
2>C:\Users\chris-pet-projects\Documents\boost-compute\test\test_device.cpp(283): error C2770: invalid explicit template argument(s) for 'detail::get_object_info_type<boost::compute::device,Enum>::type boost::compute::device::get_info(void) const'  
2>  C:\Users\chris-pet-projects\Documents\boost_1_65_1\boost/compute/device.hpp(322): note: see declaration of 'boost::compute::device::get_info'  
2>C:\Users\chris-pet-projects\Documents\boost-compute\test\test_device.cpp(283): error C2974: 'boost::compute::device::get_info': invalid template argument for 'T', type expected  
2>  C:\Users\chris-pet-projects\Documents\boost_1_65_1\boost/compute/device.hpp(314): note: see declaration of 'boost::compute::device::get_info'  
3>C:\Users\chris-pet-projects\Documents\boost-compute\test\test_kernel.cpp(174): error C2039: 'optional': is not a member of 'boost'  
3>  C:\Users\chris-pet-projects\Documents\boost_1_65_1\boost/compute/system.hpp(28): note: see declaration of 'boost'  
3>C:\Users\chris-pet-projects\Documents\boost-compute\test\test_kernel.cpp(174): error C2065: 'optional': undeclared identifier  
3>C:\Users\chris-pet-projects\Documents\boost-compute\test\test_kernel.cpp(174): error C2275: 'size_t': illegal use of this type as an expression  
3>  C:\Users\chris-pet-projects\Documents\boost-compute\test\test_kernel.cpp: note: see declaration of 'size_t'  
3>C:\Users\chris-pet-projects\Documents\boost-compute\test\test_kernel.cpp(174): error C2065: 'count': undeclared identifier  
3>C:\Users\chris-pet-projects\Documents\boost-compute\test\test_kernel.cpp(174): error C2039: 'get_sub_group_info': is not a member of 'boost::compute::kernel'  
3>  C:\Users\chris-pet-projects\Documents\boost_1_65_1\boost/compute/kernel.hpp(39): note: see declaration of 'boost::compute::kernel'  
3>C:\Users\chris-pet-projects\Documents\boost-compute\test\test_kernel.cpp(182): error C2065: 'count': undeclared identifier  
3>C:\Users\chris-pet-projects\Documents\boost-compute\test\test_kernel.cpp(187): error C2065: 'count': undeclared identifier  
3>C:\Users\chris-pet-projects\Documents\boost-compute\test\test_kernel.cpp(192): error C2065: 'count': undeclared identifier  
3>C:\Users\chris-pet-projects\Documents\boost-compute\test\test_kernel.cpp(192): error C2039: 'none': is not a member of 'boost'  
3>  C:\Users\chris-pet-projects\Documents\boost_1_65_1\boost/compute/system.hpp(28): note: see declaration of 'boost'  
3>C:\Users\chris-pet-projects\Documents\boost-compute\test\test_kernel.cpp(192): error C2065: 'none': undeclared identifier  
3>C:\Users\chris-pet-projects\Documents\boost-compute\test\test_kernel.cpp(196): error C2065: 'count': undeclared identifier  
3>C:\Users\chris-pet-projects\Documents\boost-compute\test\test_kernel.cpp(196): error C2039: 'get_sub_group_info': is not a member of 'boost::compute::kernel'  
3>  C:\Users\chris-pet-projects\Documents\boost_1_65_1\boost/compute/kernel.hpp(39): note: see declaration of 'boost::compute::kernel'  
3>C:\Users\chris-pet-projects\Documents\boost-compute\test\test_kernel.cpp(205): error C2065: 'count': undeclared identifier  
3>C:\Users\chris-pet-projects\Documents\boost-compute\test\test_kernel.cpp(210): error C2065: 'count': undeclared identifier  
3>C:\Users\chris-pet-projects\Documents\boost-compute\test\test_kernel.cpp(215): error C2065: 'count': undeclared identifier  
3>C:\Users\chris-pet-projects\Documents\boost-compute\test\test_kernel.cpp(215): error C2039: 'none': is not a member of 'boost'  
3>  C:\Users\chris-pet-projects\Documents\boost_1_65_1\boost/compute/system.hpp(28): note: see declaration of 'boost'  
3>C:\Users\chris-pet-projects\Documents\boost-compute\test\test_kernel.cpp(215): error C2065: 'none': undeclared identifier  
3>C:\Users\chris-pet-projects\Documents\boost-compute\test\test_kernel.cpp(228): error C2039: 'optional': is not a member of 'boost'  
3>  C:\Users\chris-pet-projects\Documents\boost_1_65_1\boost/compute/system.hpp(28): note: see declaration of 'boost'  
3>C:\Users\chris-pet-projects\Documents\boost-compute\test\test_kernel.cpp(228): error C2065: 'optional': undeclared identifier  
3>C:\Users\chris-pet-projects\Documents\boost-compute\test\test_kernel.cpp(228): error C2275: 'std::vector<std::size_t,std::allocator<std::_Ty>>': illegal use of this type as an expression  
3>          with  
3>          [  
3>              _Ty=std::size_t  
3>          ]  
3>  C:\Users\chris-pet-projects\Documents\boost-compute\test\test_kernel.cpp(228): note: see declaration of 'std::vector<std::size_t,std::allocator<std::_Ty>>'  
3>          with  
3>          [  
3>              _Ty=std::size_t  
3>          ]  
3>C:\Users\chris-pet-projects\Documents\boost-compute\test\test_kernel.cpp(228): error C2065: 'local_size': undeclared identifier  
3>C:\Users\chris-pet-projects\Documents\boost-compute\test\test_kernel.cpp(229): error C2039: 'get_sub_group_info': is not a member of 'boost::compute::kernel'  
3>  C:\Users\chris-pet-projects\Documents\boost_1_65_1\boost/compute/kernel.hpp(39): note: see declaration of 'boost::compute::kernel'  
3>C:\Users\chris-pet-projects\Documents\boost-compute\test\test_kernel.cpp(238): error C2065: 'local_size': undeclared identifier  
3>C:\Users\chris-pet-projects\Documents\boost-compute\test\test_kernel.cpp(239): error C2065: 'local_size': undeclared identifier  
3>C:\Users\chris-pet-projects\Documents\boost-compute\test\test_kernel.cpp(239): error C2228: left of '.value' must have class/struct/union  
3>  C:\Users\chris-pet-projects\Documents\boost-compute\test\test_kernel.cpp(239): note: type is 'unknown-type'  
3>C:\Users\chris-pet-projects\Documents\boost-compute\test\test_kernel.cpp(239): error C2228: left of '.size' must have class/struct/union  
3>C:\Users\chris-pet-projects\Documents\boost-compute\test\test_kernel.cpp(246): error C2065: 'local_size': undeclared identifier  
3>C:\Users\chris-pet-projects\Documents\boost-compute\test\test_kernel.cpp(246): error C2039: 'none': is not a member of 'boost'  
3>  C:\Users\chris-pet-projects\Documents\boost_1_65_1\boost/compute/system.hpp(28): note: see declaration of 'boost'  
3>C:\Users\chris-pet-projects\Documents\boost-compute\test\test_kernel.cpp(246): error C2065: 'none': undeclared identifier  
3>C:\Users\chris-pet-projects\Documents\boost-compute\test\test_kernel.cpp(250): error C2039: 'optional': is not a member of 'boost'  
3>  C:\Users\chris-pet-projects\Documents\boost_1_65_1\boost/compute/system.hpp(28): note: see declaration of 'boost'  
3>C:\Users\chris-pet-projects\Documents\boost-compute\test\test_kernel.cpp(250): error C2065: 'optional': undeclared identifier  
3>C:\Users\chris-pet-projects\Documents\boost-compute\test\test_kernel.cpp(250): error C2275: 'size_t': illegal use of this type as an expression  
3>  C:\Users\chris-pet-projects\Documents\boost-compute\test\test_kernel.cpp: note: see declaration of 'size_t'  
3>C:\Users\chris-pet-projects\Documents\boost-compute\test\test_kernel.cpp(250): error C2065: 'local_size_simple': undeclared identifier  
3>C:\Users\chris-pet-projects\Documents\boost-compute\test\test_kernel.cpp(251): error C2039: 'get_sub_group_info': is not a member of 'boost::compute::kernel'  
3>  C:\Users\chris-pet-projects\Documents\boost_1_65_1\boost/compute/kernel.hpp(39): note: see declaration of 'boost::compute::kernel'  
3>C:\Users\chris-pet-projects\Documents\boost-compute\test\test_kernel.cpp(260): error C2065: 'local_size_simple': undeclared identifier  
3>C:\Users\chris-pet-projects\Documents\boost-compute\test\test_kernel.cpp(267): error C2065: 'local_size_simple': undeclared identifier  
3>C:\Users\chris-pet-projects\Documents\boost-compute\test\test_kernel.cpp(267): error C2039: 'none': is not a member of 'boost'  
3>  C:\Users\chris-pet-projects\Documents\boost_1_65_1\boost/compute/system.hpp(28): note: see declaration of 'boost'  
3>C:\Users\chris-pet-projects\Documents\boost-compute\test\test_kernel.cpp(267): error C2065: 'none': undeclared identifier  
3>C:\Users\chris-pet-projects\Documents\boost-compute\test\test_kernel.cpp(271): error C2039: 'optional': is not a member of 'boost'  
3>  C:\Users\chris-pet-projects\Documents\boost_1_65_1\boost/compute/system.hpp(28): note: see declaration of 'boost'  
3>C:\Users\chris-pet-projects\Documents\boost-compute\test\test_kernel.cpp(271): error C2065: 'optional': undeclared identifier  
3>C:\Users\chris-pet-projects\Documents\boost-compute\test\test_kernel.cpp(271): error C2275: 'size_t': illegal use of this type as an expression  
3>  C:\Users\chris-pet-projects\Documents\boost-compute\test\test_kernel.cpp: note: see declaration of 'size_t'  
3>C:\Users\chris-pet-projects\Documents\boost-compute\test\test_kernel.cpp(271): error C2065: 'max': undeclared identifier  
3>C:\Users\chris-pet-projects\Documents\boost-compute\test\test_kernel.cpp(272): error C2039: 'get_sub_group_info': is not a member of 'boost::compute::kernel'  
3>  C:\Users\chris-pet-projects\Documents\boost_1_65_1\boost/compute/kernel.hpp(39): note: see declaration of 'boost::compute::kernel'  
3>C:\Users\chris-pet-projects\Documents\boost-compute\test\test_kernel.cpp(280): error C2065: 'max': undeclared identifier  
3>C:\Users\chris-pet-projects\Documents\boost-compute\test\test_kernel.cpp(287): error C2065: 'max': undeclared identifier  
3>C:\Users\chris-pet-projects\Documents\boost-compute\test\test_kernel.cpp(287): error C2039: 'none': is not a member of 'boost'  
3>  C:\Users\chris-pet-projects\Documents\boost_1_65_1\boost/compute/system.hpp(28): note: see declaration of 'boost'  
3>C:\Users\chris-pet-projects\Documents\boost-compute\test\test_kernel.cpp(287): error C2065: 'none': undeclared identifier  
3>C:\Users\chris-pet-projects\Documents\boost-compute\test\test_kernel.cpp(295): error C2039: 'check_version': is not a member of 'boost::compute::platform'  
3>  C:\Users\chris-pet-projects\Documents\boost_1_65_1\boost/compute/platform.hpp(38): note: see declaration of 'boost::compute::platform'  
3>C:\Users\chris-pet-projects\Documents\boost-compute\test\test_kernel.cpp(306): error C2039: 'clone': is not a member of 'boost::compute::kernel'  
3>  C:\Users\chris-pet-projects\Documents\boost_1_65_1\boost/compute/kernel.hpp(39): note: see declaration of 'boost::compute::kernel'  
4>C:\Users\chris-pet-projects\Documents\boost-compute\test\test_program.cpp(149): error C2039: 'create_with_il_file': is not a member of 'boost::compute::program'  
4>  C:\Users\chris-pet-projects\Documents\boost_1_65_1\boost/compute/program.hpp(74): note: see declaration of 'boost::compute::program'  
4>C:\Users\chris-pet-projects\Documents\boost-compute\test\test_program.cpp(145): error C3861: 'create_with_il_file': identifier not found  
4>C:\Users\chris-pet-projects\Documents\boost-compute\test\test_program.cpp(175): error C2039: 'create_with_il_file': is not a member of 'boost::compute::program'  
4>  C:\Users\chris-pet-projects\Documents\boost_1_65_1\boost/compute/program.hpp(74): note: see declaration of 'boost::compute::program'  
4>C:\Users\chris-pet-projects\Documents\boost-compute\test\test_program.cpp(171): error C3861: 'create_with_il_file': identifier not found  
4>C:\Users\chris-pet-projects\Documents\boost-compute\test\test_program.cpp(179): error C2039: 'il_binary': is not a member of 'boost::compute::program'  
4>  C:\Users\chris-pet-projects\Documents\boost_1_65_1\boost/compute/program.hpp(74): note: see declaration of 'boost::compute::program'  
4>C:\Users\chris-pet-projects\Documents\boost-compute\test\test_program.cpp(184): error C2039: 'create_with_il': is not a member of 'boost::compute::program'  
4>  C:\Users\chris-pet-projects\Documents\boost_1_65_1\boost/compute/program.hpp(74): note: see declaration of 'boost::compute::program'  
4>C:\Users\chris-pet-projects\Documents\boost-compute\test\test_program.cpp(184): error C2440: '<function-style-cast>': cannot convert from 'initializer list' to 'compute_test::create_with_il'  
4>  C:\Users\chris-pet-projects\Documents\boost-compute\test\test_program.cpp(184): note: No constructor could take the source type, or constructor overload resolution was ambiguous  
4>C:\Users\chris-pet-projects\Documents\boost-compute\test\test_program.cpp(200): error C2039: 'il_binary': is not a member of 'boost::compute::program'  
4>  C:\Users\chris-pet-projects\Documents\boost_1_65_1\boost/compute/program.hpp(74): note: see declaration of 'boost::compute::program'  
1>C:\Users\chris-pet-projects\Documents\boost-compute\test\test_command_queue.cpp(328): error C2672: 'boost::compute::command_queue::get_info': no matching overloaded function found  
1>C:\Users\chris-pet-projects\Documents\boost-compute\test\test_command_queue.cpp(328): error C2770: invalid explicit template argument(s) for 'detail::get_object_info_type<boost::compute::command_queue,Enum>::type boost::compute::command_queue::get_info(void) const'  
1>  C:\Users\chris-pet-projects\Documents\boost_1_65_1\boost/compute/command_queue.hpp(248): note: see declaration of 'boost::compute::command_queue::get_info'  
1>C:\Users\chris-pet-projects\Documents\boost-compute\test\test_command_queue.cpp(328): error C2974: 'boost::compute::command_queue::get_info': invalid template argument for 'T', type expected  
1>  C:\Users\chris-pet-projects\Documents\boost_1_65_1\boost/compute/command_queue.hpp(240): note: see declaration of 'boost::compute::command_queue::get_info'  
1>C:\Users\chris-pet-projects\Documents\boost-compute\test\test_command_queue.cpp(332): error C2039: 'get_default_device_queue': is not a member of 'boost::compute::command_queue'  
1>  C:\Users\chris-pet-projects\Documents\boost_1_65_1\boost/compute/command_queue.hpp(79): note: see declaration of 'boost::compute::command_queue'  
1>C:\Users\chris-pet-projects\Documents\boost-compute\test\test_command_queue.cpp(332): error C2672: 'boost::test_tools::tt_detail::check_frwd': no matching overloaded function found  
1>C:\Users\chris-pet-projects\Documents\boost-compute\test\test_command_queue.cpp(332): error C2780: 'bool boost::test_tools::tt_detail::check_frwd(Pred,const boost::unit_test::lazy_ostream &,boost::unit_test::const_string,std::size_t,boost::test_tools::tt_detail::tool_level,boost::test_tools::tt_detail::check_type,const Arg0 &,const char *,const Arg1 &,const char *,const Arg2 &,const char *,const Arg3 &,const char *,const Arg4 &,const char *)': expects 16 arguments - 9 provided  
1>  C:\Users\chris-pet-projects\Documents\boost_1_65_1\boost/test/tools/old/impl.hpp(92): note: see declaration of 'boost::test_tools::tt_detail::check_frwd'  
1>C:\Users\chris-pet-projects\Documents\boost-compute\test\test_command_queue.cpp(332): error C2780: 'bool boost::test_tools::tt_detail::check_frwd(Pred,const boost::unit_test::lazy_ostream &,boost::unit_test::const_string,std::size_t,boost::test_tools::tt_detail::tool_level,boost::test_tools::tt_detail::check_type,const Arg0 &,const char *,const Arg1 &,const char *,const Arg2 &,const char *,const Arg3 &,const char *)': expects 14 arguments - 9 provided  
1>  C:\Users\chris-pet-projects\Documents\boost_1_65_1\boost/test/tools/old/impl.hpp(92): note: see declaration of 'boost::test_tools::tt_detail::check_frwd'  
1>C:\Users\chris-pet-projects\Documents\boost-compute\test\test_command_queue.cpp(332): error C2780: 'bool boost::test_tools::tt_detail::check_frwd(Pred,const boost::unit_test::lazy_ostream &,boost::unit_test::const_string,std::size_t,boost::test_tools::tt_detail::tool_level,boost::test_tools::tt_detail::check_type,const Arg0 &,const char *,const Arg1 &,const char *,const Arg2 &,const char *)': expects 12 arguments - 9 provided  
1>  C:\Users\chris-pet-projects\Documents\boost_1_65_1\boost/test/tools/old/impl.hpp(92): note: see declaration of 'boost::test_tools::tt_detail::check_frwd'  
1>C:\Users\chris-pet-projects\Documents\boost-compute\test\test_command_queue.cpp(332): error C2780: 'bool boost::test_tools::tt_detail::check_frwd(Pred,const boost::unit_test::lazy_ostream &,boost::unit_test::const_string,std::size_t,boost::test_tools::tt_detail::tool_level,boost::test_tools::tt_detail::check_type,const Arg0 &,const char *,const Arg1 &,const char *)': expects 10 arguments - 9 provided  
1>  C:\Users\chris-pet-projects\Documents\boost_1_65_1\boost/test/tools/old/impl.hpp(92): note: see declaration of 'boost::test_tools::tt_detail::check_frwd'  
1>C:\Users\chris-pet-projects\Documents\boost-compute\test\test_command_queue.cpp(332): error C2780: 'bool boost::test_tools::tt_detail::check_frwd(Pred,const boost::unit_test::lazy_ostream &,boost::unit_test::const_string,std::size_t,boost::test_tools::tt_detail::tool_level,boost::test_tools::tt_detail::check_type,const Arg0 &,const char *)': expects 8 arguments - 9 provided  
1>  C:\Users\chris-pet-projects\Documents\boost_1_65_1\boost/test/tools/old/impl.hpp(92): note: see declaration of 'boost::test_tools::tt_detail::check_frwd'  
1>C:\Users\chris-pet-projects\Documents\boost-compute\test\test_command_queue.cpp(344): error C2039: 'set_as_default_device_queue': is not a member of 'boost::compute::command_queue'  
1>  C:\Users\chris-pet-projects\Documents\boost_1_65_1\boost/compute/command_queue.hpp(79): note: see declaration of 'boost::compute::command_queue'  
1>C:\Users\chris-pet-projects\Documents\boost-compute\test\test_command_queue.cpp(348): error C2039: 'get_default_device_queue': is not a member of 'boost::compute::command_queue'  
1>  C:\Users\chris-pet-projects\Documents\boost_1_65_1\boost/compute/command_queue.hpp(79): note: see declaration of 'boost::compute::command_queue'  
1>C:\Users\chris-pet-projects\Documents\boost-compute\test\test_command_queue.cpp(348): error C2672: 'boost::test_tools::tt_detail::check_frwd': no matching overloaded function found  
1>C:\Users\chris-pet-projects\Documents\boost-compute\test\test_command_queue.cpp(348): error C2780: 'bool boost::test_tools::tt_detail::check_frwd(Pred,const boost::unit_test::lazy_ostream &,boost::unit_test::const_string,std::size_t,boost::test_tools::tt_detail::tool_level,boost::test_tools::tt_detail::check_type,const Arg0 &,const char *,const Arg1 &,const char *,const Arg2 &,const char *,const Arg3 &,const char *,const Arg4 &,const char *)': expects 16 arguments - 9 provided  
1>  C:\Users\chris-pet-projects\Documents\boost_1_65_1\boost/test/tools/old/impl.hpp(92): note: see declaration of 'boost::test_tools::tt_detail::check_frwd'  
1>C:\Users\chris-pet-projects\Documents\boost-compute\test\test_command_queue.cpp(348): error C2780: 'bool boost::test_tools::tt_detail::check_frwd(Pred,const boost::unit_test::lazy_ostream &,boost::unit_test::const_string,std::size_t,boost::test_tools::tt_detail::tool_level,boost::test_tools::tt_detail::check_type,const Arg0 &,const char *,const Arg1 &,const char *,const Arg2 &,const char *,const Arg3 &,const char *)': expects 14 arguments - 9 provided  
1>  C:\Users\chris-pet-projects\Documents\boost_1_65_1\boost/test/tools/old/impl.hpp(92): note: see declaration of 'boost::test_tools::tt_detail::check_frwd'  
1>C:\Users\chris-pet-projects\Documents\boost-compute\test\test_command_queue.cpp(348): error C2780: 'bool boost::test_tools::tt_detail::check_frwd(Pred,const boost::unit_test::lazy_ostream &,boost::unit_test::const_string,std::size_t,boost::test_tools::tt_detail::tool_level,boost::test_tools::tt_detail::check_type,const Arg0 &,const char *,const Arg1 &,const char *,const Arg2 &,const char *)': expects 12 arguments - 9 provided  
1>  C:\Users\chris-pet-projects\Documents\boost_1_65_1\boost/test/tools/old/impl.hpp(92): note: see declaration of 'boost::test_tools::tt_detail::check_frwd'  
1>C:\Users\chris-pet-projects\Documents\boost-compute\test\test_command_queue.cpp(348): error C2780: 'bool boost::test_tools::tt_detail::check_frwd(Pred,const boost::unit_test::lazy_ostream &,boost::unit_test::const_string,std::size_t,boost::test_tools::tt_detail::tool_level,boost::test_tools::tt_detail::check_type,const Arg0 &,const char *,const Arg1 &,const char *)': expects 10 arguments - 9 provided  
1>  C:\Users\chris-pet-projects\Documents\boost_1_65_1\boost/test/tools/old/impl.hpp(92): note: see declaration of 'boost::test_tools::tt_detail::check_frwd'  
1>C:\Users\chris-pet-projects\Documents\boost-compute\test\test_command_queue.cpp(348): error C2780: 'bool boost::test_tools::tt_detail::check_frwd(Pred,const boost::unit_test::lazy_ostream &,boost::unit_test::const_string,std::size_t,boost::test_tools::tt_detail::tool_level,boost::test_tools::tt_detail::check_type,const Arg0 &,const char *)': expects 8 arguments - 9 provided  
1>  C:\Users\chris-pet-projects\Documents\boost_1_65_1\boost/test/tools/old/impl.hpp(92): note: see declaration of 'boost::test_tools::tt_detail::check_frwd'  
6>C:\Users\chris-pet-projects\Documents\boost-compute\test\test_svm_ptr.cpp(124): error C2039: 'enqueue_svm_migrate_memory': is not a member of 'boost::compute::command_queue'  
6>  C:\Users\chris-pet-projects\Documents\boost_1_65_1\boost/compute/command_queue.hpp(79): note: see declaration of 'boost::compute::command_queue'  
6>C:\Users\chris-pet-projects\Documents\boost-compute\test\test_svm_ptr.cpp(124): error C2228: left of '.wait' must have class/struct/union  
6>C:\Users\chris-pet-projects\Documents\boost-compute\test\test_svm_ptr.cpp(142): error C2039: 'enqueue_svm_migrate_memory': is not a member of 'boost::compute::command_queue'  
6>  C:\Users\chris-pet-projects\Documents\boost_1_65_1\boost/compute/command_queue.hpp(79): note: see declaration of 'boost::compute::command_queue'  
6>C:\Users\chris-pet-projects\Documents\boost-compute\test\test_svm_ptr.cpp(144): error C2039: 'migrate_to_host': is not a member of 'boost::compute::command_queue'  
6>  C:\Users\chris-pet-projects\Documents\boost_1_65_1\boost/compute/command_queue.hpp(79): note: see declaration of 'boost::compute::command_queue'  
6>C:\Users\chris-pet-projects\Documents\boost-compute\test\test_svm_ptr.cpp(144): error C2065: 'migrate_to_host': undeclared identifier  
6>C:\Users\chris-pet-projects\Documents\boost-compute\test\test_svm_ptr.cpp(144): error C2228: left of '.wait' must have class/struct/union  
5>C:\Users\chris-pet-projects\Documents\boost-compute\test\test_strided_iterator.cpp(176): error C2338: ( boost::is_same< std::iterator_traits< i_type >::iterator_category, std::iterator_traits< bc::strided_iterator<i_type> >::iterator_category >::value )

---

## Comment 1

> Username: jszuppe  
> Created at: 2017-11-12 22:20:47 UTC  
> Url: https://github.com/boostorg/compute/issues/751#issuecomment-343772962  

I don't have much free time right now, but maybe I'll find time for that later this week. Boost.Compute compiles ok on VS'15 and VS'13: https://ci.appveyor.com/project/jszuppe/compute, so maybe it's some incompatibility with other Boost libraries in 1.65.1 package. It also worked fine on my GTX1080 some time ago. Can you post drivers you used?

---

## Comment 2

> Username: Kristian-Popov  
> Created at: 2017-11-14 18:50:17 UTC  
> Url: https://github.com/boostorg/compute/issues/751#issuecomment-344358848  

Sorry, compilation errors are my mistake. Since Boost compute needs other Boost libraries, I've pointed CMake to folder where a fully built Boost distribution is but it contained another copy of Boost compute (possibly even another version). This was a reason for a header mess and compilation errors. After removing an extra copy of Boost compute library I got all tests compiled. Vector errors are gone, but few others left.  
### Drivers version: 384.77  
  
11: Test command: C:\Users\chris-pet-projects\Documents\boost-compute-build\test\Release\test_program.exe  
  
11: Test timeout computed to be: 9.99988e+006  
11: Running 14 test cases...  
11: unknown location(0): fatal error: in "compute_test/create_with_il": memory access violation  
11: C:\Users\chris-pet-projects\Documents\boost-compute\test\test_program.cpp(131): last checkpoint: "create_with_il" test entry  
11:   
11: *** 1 failure is detected in the test module "TestProgram"  
  
5: Test command: C:\Users\chris-pet-projects\Documents\boost-compute-build\test\Release\test_device.exe  
  
5: Test timeout computed to be: 9.99988e+006  
5: Running 14 test cases...  
5: skipping test: device does not support CL_DEVICE_PARTITION_EQUALLY  
5: skipping test: device does not support CL_DEVICE_PARTITION_BY_COUNTS  
5: skipping test: device does not support partitioning by affinity domain  
5: GeForce GTX 1050 Ti  
5: unknown location(0): fatal error: in "get_host_timer": memory access violation  
5: C:\Users\chris-pet-projects\Documents\boost-compute\test\test_device.cpp(240): last checkpoint  
5:   
5: *** 1 failure is detected in the test module "TestDevice"  
  
Often fixing a reason of a memory access problems is very difficult, but I don't think it is a case. In both cases when running these tests in a debugger I get "Access violation executing location 0x0000000000000000" when calling methods like clGetHostTimer and clCreateProgramWithIL. And I'm not surprised - since default device in NVidia GPU that doesn't support these new API methods (in my case NVidia GPU supports OpenCL 1.2 only as clinfo app showed), that methods are simply not implemented for this device. But other devices are Intel CPU and GPU that indeed support new API, so it's not an error that new API calls are enabled.  
As a conclusion, I feel that enabling of these methods (and possibly other newly added methods) at a compile time is not enough, a runtime check on device capabilities should be added.

---

## Comment 3

> Username: jszuppe  
> Created at: 2017-11-14 19:21:12 UTC  
> Updated at: 2017-11-14 19:21:39 UTC  
> Url: https://github.com/boostorg/compute/issues/751#issuecomment-344368104  

Thanks for the update. I'll fix that tomorrow.

---

## Comment 4

> Username: jszuppe  
> Created at: 2017-11-16 22:05:21 UTC  
> Url: https://github.com/boostorg/compute/issues/751#issuecomment-345077858  

One hint: If you want to use the latest OpenCL headers, but you devices don't support that version (like in this case you use 2.1 headers, but NV supports only 1.2), I recommend defining macro `BOOST_COMPUTE_MAX_CL_VERSION` (for example to `102` which means OpenCL 1.2). If you have devices with different OpenCL versions then it's on you to check if device support given OpenCL API before using it. (Of course, it has nothing to do with that bug in tests, there's still a bug and thanks for reporting it.)

---

## Comment 5

> Username: jszuppe  
> Created at: 2017-11-17 07:59:24 UTC  
> Url: https://github.com/boostorg/compute/issues/751#issuecomment-345171900  

Should be fixed by #753
