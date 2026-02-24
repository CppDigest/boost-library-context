# #413 - boost::python::to_python_value<bool const&> generates stack-buffer-overflow in address sanitizer [Closed]

> Username: o01eg  
> Created at: 2023-02-21 07:04:03 UTC  
> Updated at: 2025-03-19 19:03:25 UTC  
> Closed at: 2025-03-19 19:03:24 UTC  
> Url: https://github.com/boostorg/python/issues/413  

```  
==32076==ERROR: AddressSanitizer: stack-buffer-overflow on address 0x7f49c5afa450 at pc 0x7f49cc04987a bp 0x7f49c5af8ea0 sp 0x7f49c5af8e98  
READ of size 1 at 0x7f49c5afa450 thread T1  
    #0 0x7f49cc049879 in boost::python::to_python_value<bool const&>::operator()(bool const&) const /usr/include/boost/python/converter/builtin_converters.hpp:113  
    #1 0x7f49cc049879 in _object* boost::python::detail::invoke<boost::python::to_python_value<bool const&>, boost::python::detail::datum<bool const> >(boost::python::detail::invoke_tag_<false, false>, boost::python::to_python_value<bool const&> const&, boost::python::detail::datum<bool const>&) /usr/include/boost/python/detail/invoke.hpp:73  
    #2 0x7f49cc049879 in boost::python::detail::caller_arity<0u>::impl<boost::python::detail::datum<bool const>, boost::python::return_value_policy<boost::python::return_by_value, boost::python::default_call_policies>, boost::mpl::vector1<bool const&> >::operator()(_object*, _object*) /usr/include/boost/python/detail/caller.hpp:233  
    #3 0x7f49cc049879 in boost::python::objects::caller_py_function_impl<boost::python::detail::caller<boost::python::detail::datum<bool const>, boost::python::return_value_policy<boost::python::return_by_value, boost::python::default_call_policies>, boost::mpl::vector1<bool const&> > >::operator()(_object*, _object*) /usr/include/boost/python/object/py_function.hpp:38  
    #4 0x7f49ce5e135c in boost::python::objects::function::call(_object*, _object*) const (/usr/lib/x86_64-linux-gnu/libboost_python311.so.1.74.0+0x2035c)  
    #5 0x7f49ce5e1527  (/usr/lib/x86_64-linux-gnu/libboost_python311.so.1.74.0+0x20527)  
    #6 0x7f49ce5e649a in boost::python::detail::exception_handler::operator()(boost::function0<void> const&) const (/usr/lib/x86_64-linux-gnu/libboost_python311.so.1.74.0+0x2549a)  
    #7 0x7f49cc01b5a5 in operator() /usr/include/boost/python/detail/translate_exception.hpp:46  
    #8 0x7f49cc01b5a5 in operator()<bool, boost::python::detail::translate_exception<(anonymous namespace)::import_error, void (*)(const (anonymous namespace)::import_error&)>, boost::_bi::rrlist2<const boost::python::detail::exception_handler&, const boost::function0<void>&> > /usr/include/boost/bind/bind.hpp:388  
    #9 0x7f49cc01b5a5 in operator()<const boost::python::detail::exception_handler&, const boost::function0<void>&> /usr/include/boost/bind/bind.hpp:1318  
    #10 0x7f49cc01b5a5 in invoke /usr/include/boost/function/function_template.hpp:137  
    #11 0x7f49ce5e63ac in boost::python::handle_exception_impl(boost::function0<void>) (/usr/lib/x86_64-linux-gnu/libboost_python311.so.1.74.0+0x253ac)  
    #12 0x7f49ce5deec1  (/usr/lib/x86_64-linux-gnu/libboost_python311.so.1.74.0+0x1dec1)  
    #13 0x7f49c997baec in _PyObject_MakeTpCall (/usr/lib/x86_64-linux-gnu/libpython3.11.so.1.0+0x17baec)  
    #14 0x7f49c997d568  (/usr/lib/x86_64-linux-gnu/libpython3.11.so.1.0+0x17d568)  
    #15 0x7f49c997d68d in PyObject_CallFunction (/usr/lib/x86_64-linux-gnu/libpython3.11.so.1.0+0x17d68d)  
    #16 0x7f49c99d0438 in _PyObject_GenericGetAttrWithDict (/usr/lib/x86_64-linux-gnu/libpython3.11.so.1.0+0x1d0438)  
    #17 0x7f49c99cf977 in PyObject_GetAttr (/usr/lib/x86_64-linux-gnu/libpython3.11.so.1.0+0x1cf977)  
    #18 0x7f49c990682b in _PyEval_EvalFrameDefault (/usr/lib/x86_64-linux-gnu/libpython3.11.so.1.0+0x10682b)  
    #19 0x7f49c9a6a6d9  (/usr/lib/x86_64-linux-gnu/libpython3.11.so.1.0+0x26a6d9)  
    #20 0x7f49c997e1ce  (/usr/lib/x86_64-linux-gnu/libpython3.11.so.1.0+0x17e1ce)  
    #21 0x7f49c997e3e9 in PyObject_CallMethodObjArgs (/usr/lib/x86_64-linux-gnu/libpython3.11.so.1.0+0x17e3e9)  
    #22 0x7f49c9a9637e in PyImport_ImportModuleLevelObject (/usr/lib/x86_64-linux-gnu/libpython3.11.so.1.0+0x29637e)  
    #23 0x7f49c9a6185d  (/usr/lib/x86_64-linux-gnu/libpython3.11.so.1.0+0x26185d)  
    #24 0x7f49c99c9c51  (/usr/lib/x86_64-linux-gnu/libpython3.11.so.1.0+0x1c9c51)  
    #25 0x7f49c990a7d2 in _PyEval_EvalFrameDefault (/usr/lib/x86_64-linux-gnu/libpython3.11.so.1.0+0x10a7d2)  
    #26 0x7f49c9a6a6d9  (/usr/lib/x86_64-linux-gnu/libpython3.11.so.1.0+0x26a6d9)  
    #27 0x7f49c997e1ce  (/usr/lib/x86_64-linux-gnu/libpython3.11.so.1.0+0x17e1ce)  
    #28 0x7f49c997e3e9 in PyObject_CallMethodObjArgs (/usr/lib/x86_64-linux-gnu/libpython3.11.so.1.0+0x17e3e9)  
    #29 0x7f49c9a9637e in PyImport_ImportModuleLevelObject (/usr/lib/x86_64-linux-gnu/libpython3.11.so.1.0+0x29637e)  
    #30 0x7f49c9913377 in _PyEval_EvalFrameDefault (/usr/lib/x86_64-linux-gnu/libpython3.11.so.1.0+0x113377)  
    #31 0x7f49c9bcc73b  (/usr/lib/x86_64-linux-gnu/libpython3.11.so.1.0+0x3cc73b)  
    #32 0x7f49c9a68420 in PyEval_EvalCode (/usr/lib/x86_64-linux-gnu/libpython3.11.so.1.0+0x268420)  
    #33 0x7f49c9ab6d88  (/usr/lib/x86_64-linux-gnu/libpython3.11.so.1.0+0x2b6d88)  
    #34 0x7f49c9ab7f3a in PyRun_StringFlags (/usr/lib/x86_64-linux-gnu/libpython3.11.so.1.0+0x2b7f3a)  
    #35 0x7f49ce5e9e08 in boost::python::exec(char const*, boost::python::api::object, boost::python::api::object) (/usr/lib/x86_64-linux-gnu/libboost_python311.so.1.74.0+0x28e08)  
    #36 0x7f49cc02b1f1 in PythonParser::ParseFileCommon(boost::filesystem::path const&, boost::python::dict const&, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >&, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >&) const /home/oleg/devel/freeorion/parse/PythonParser.cpp:326  
    #37 0x7f49cbe14bfd in parse_file<(anonymous namespace)::py_grammar_techs> /home/oleg/devel/freeorion/parse/PythonParserImpl.h:19  
    #38 0x7f49cbe14bfd in std::tuple<boost::multi_index::multi_index_container<std::unique_ptr<Tech, std::default_delete<Tech> >, boost::multi_index::indexed_by<boost::multi_index::ordered_non_unique<boost::multi_index::tag<TechManager::CategoryIndex, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na>, boost::multi_index::const_mem_fun<Tech, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > const&, &(Tech::Category[abi:cxx11]() const)>, mpl_::na>, boost::multi_index::ordered_unique<boost::multi_index::tag<TechManager::NameIndex, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na>, boost::multi_index::const_mem_fun<Tech, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > const&, &(Tech::Name[abi:cxx11]() const)>, mpl_::na>, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na>, std::allocator<std::unique_ptr<Tech, std::default_delete<Tech> > > >, std::map<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, std::unique_ptr<TechCategory, std::default_delete<TechCategory> >, std::less<void>, std::allocator<std::pair<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > const, std::unique_ptr<TechCategory, std::default_delete<TechCategory> > > > >, std::set<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, std::less<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > >, std::allocator<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > > > > parse::techs<std::tuple<boost::multi_index::multi_index_container<std::unique_ptr<Tech, std::default_delete<Tech> >, boost::multi_index::indexed_by<boost::multi_index::ordered_non_unique<boost::multi_index::tag<TechManager::CategoryIndex, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na>, boost::multi_index::const_mem_fun<Tech, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > const&, &(Tech::Category[abi:cxx11]() const)>, mpl_::na>, boost::multi_index::ordered_unique<boost::multi_index::tag<TechManager::NameIndex, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na>, boost::multi_index::const_mem_fun<Tech, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > const&, &(Tech::Name[abi:cxx11]() const)>, mpl_::na>, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na>, std::allocator<std::unique_ptr<Tech, std::default_delete<Tech> > > >, std::map<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, std::unique_ptr<TechCategory, std::default_delete<TechCategory> >, std::less<void>, std::allocator<std::pair<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > const, std::unique_ptr<TechCategory, std::default_delete<TechCategory> > > > >, std::set<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, std::less<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > >, std::allocator<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > > > > >(PythonParser const&, boost::filesystem::path const&) /home/oleg/devel/freeorion/parse/TechsParser.cpp:202  
    #39 0x7f49cd7d8cd0 in Pending::Pending<decltype ({parm#1}({parm#2}, {parm#3}))> Pending::ParseSynchronously<std::tuple<boost::multi_index::multi_index_container<std::unique_ptr<Tech, std::default_delete<Tech> >, boost::multi_index::indexed_by<boost::multi_index::ordered_non_unique<boost::multi_index::tag<TechManager::CategoryIndex, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na>, boost::multi_index::const_mem_fun<Tech, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > const&, &(Tech::Category[abi:cxx11]() const)>, mpl_::na>, boost::multi_index::ordered_unique<boost::multi_index::tag<TechManager::NameIndex, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na>, boost::multi_index::const_mem_fun<Tech, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > const&, &(Tech::Name[abi:cxx11]() const)>, mpl_::na>, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na>, std::allocator<std::unique_ptr<Tech, std::default_delete<Tech> > > >, std::map<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, std::unique_ptr<TechCategory, std::default_delete<TechCategory> >, std::less<void>, std::allocator<std::pair<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > const, std::unique_ptr<TechCategory, std::default_delete<TechCategory> > > > >, std::set<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, std::less<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > >, std::allocator<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > > > > (PythonParser const&, boost::filesystem::path const&), PythonParser>(std::tuple<boost::multi_index::multi_index_container<std::unique_ptr<Tech, std::default_delete<Tech> >, boost::multi_index::indexed_by<boost::multi_index::ordered_non_unique<boost::multi_index::tag<TechManager::CategoryIndex, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na>, boost::multi_index::const_mem_fun<Tech, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > const&, &(Tech::Category[abi:cxx11]() const)>, mpl_::na>, boost::multi_index::ordered_unique<boost::multi_index::tag<TechManager::NameIndex, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na>, boost::multi_index::const_mem_fun<Tech, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > const&, &(Tech::Name[abi:cxx11]() const)>, mpl_::na>, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na>, std::allocator<std::unique_ptr<Tech, std::default_delete<Tech> > > >, std::map<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, std::unique_ptr<TechCategory, std::default_delete<TechCategory> >, std::less<void>, std::allocator<std::pair<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > const, std::unique_ptr<TechCategory, std::default_delete<TechCategory> > > > >, std::set<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, std::less<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > >, std::allocator<std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > > > > ( const&)(PythonParser const&, boost::filesystem::path const&), PythonParser const&, boost::filesystem::path const&, std::promise<void>&&) /home/oleg/devel/freeorion/util/../universe/../util/Pending.h:147  
    #40 0x7f49cd7b608d in IApp::StartBackgroundParsing(PythonParser const&, std::promise<void>&&) /home/oleg/devel/freeorion/util/AppInterface.cpp:127  
    #41 0x5565d5b78d3a in operator()<std::promise<void> > /home/oleg/devel/freeorion/test/system/ClientAppFixture.cpp:49  
    #42 0x5565d5b79253 in __invoke_impl<void, ClientAppFixture::ClientAppFixture()::<lambda(auto:49)>, std::promise<void> > /usr/include/c++/12/bits/invoke.h:61  
    #43 0x5565d5b79253 in __invoke<ClientAppFixture::ClientAppFixture()::<lambda(auto:49)>, std::promise<void> > /usr/include/c++/12/bits/invoke.h:96  
    #44 0x5565d5b79253 in _M_invoke<0, 1> /usr/include/c++/12/bits/std_thread.h:252  
    #45 0x5565d5b79253 in operator() /usr/include/c++/12/bits/std_thread.h:259  
    #46 0x5565d5b79253 in _M_run /usr/include/c++/12/bits/std_thread.h:210  
    #47 0x7f49c94d44a2  (/usr/lib/x86_64-linux-gnu/libstdc++.so.6+0xd44a2)  
    #48 0x7f49c96a7fd3 in start_thread nptl/pthread_create.c:442  
    #49 0x7f49c972866b in clone3 ../sysdeps/unix/sysv/linux/x86_64/clone3.S:81  
  
Address 0x7f49c5afa450 is located in stack of thread T1 at offset 1376 in frame  
    #0 0x7f49cc02a77f in PythonParser::ParseFileCommon(boost::filesystem::path const&, boost::python::dict const&, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >&, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >&) const /home/oleg/devel/freeorion/parse/PythonParser.cpp:316  
  
  This frame has 54 object(s):  
    [32, 33) '<unknown>'  
    [48, 49) '<unknown>'  
    [64, 65) '<unknown>'  
    [80, 81) '<unknown>'  
    [96, 97) '<unknown>'  
    [112, 113) 'lock'  
    [128, 132) '<unknown>'  
    [144, 148) '<unknown>'  
    [160, 164) '<unknown>'  
    [176, 180) '<unknown>'  
    [192, 196) '<unknown>'  
    [208, 212) '<unknown>'  
    [224, 228) '<unknown>'  
    [240, 244) '<unknown>'  
    [256, 260) '<unknown>'  
    [272, 276) '<unknown>'  
    [288, 296) '<unknown>'  
    [320, 328) '<unknown>'  
    [352, 360) '<unknown>'  
    [384, 392) '<unknown>'  
    [416, 424) '<unknown>'  
    [448, 456) '_boost_log_record_329' (line 329)  
    [480, 488) '<unknown>'  
    [512, 520) '<unknown>'  
    [544, 552) '_boost_log_record_331' (line 331)  
    [576, 584) '<unknown>'  
    [608, 616) '<unknown>'  
    [640, 648) '_boost_log_record_333' (line 333)  
    [672, 680) '<unknown>'  
    [704, 712) '<unknown>'  
    [736, 744) '_boost_log_record_335' (line 335)  
    [768, 776) '<unknown>'  
    [800, 808) '<unknown>'  
    [832, 840) '<unknown>'  
    [864, 872) '<unknown>'  
    [896, 904) '<unknown>'  
    [928, 936) '<unknown>'  
    [960, 968) '_boost_log_record_321' (line 321)  
    [992, 1000) 'cleanup'  
    [1024, 1040) '<unknown>'  
    [1056, 1072) '<unknown>'  
    [1088, 1104) '<unknown>'  
    [1120, 1136) '<unknown>'  
    [1152, 1168) '<unknown>'  
    [1184, 1200) '<unknown>'  
    [1216, 1232) '<unknown>'  
    [1248, 1264) '<unknown>'  
    [1280, 1296) '<unknown>'  
    [1312, 1328) '<unknown>'  
    [1344, 1368) '<unknown>' <== Memory access at offset 1376 overflows this variable  
    [1408, 1432) '<unknown>'  
    [1472, 1496) '<unknown>'  
    [1536, 1560) '<unknown>'  
    [1600, 1624) '<unknown>'  
HINT: this may be a false positive if your program uses some custom stack unwind mechanism, swapcontext or vfork  
      (longjmp and C++ exceptions *are* supported)  
Thread T1 created by T0 here:  
    #0 0x7f49ce649726 in __interceptor_pthread_create ../../../../src/libsanitizer/asan/asan_interceptors.cpp:207  
    #1 0x7f49c94d4578 in std::thread::_M_start_thread(std::unique_ptr<std::thread::_State, std::default_delete<std::thread::_State> >, void (*)()) (/usr/lib/x86_64-linux-gnu/libstdc++.so.6+0xd4578)  
    #2 0x5565d5caa22a in SmokeTestGame::host_server::host_server() /home/oleg/devel/freeorion/test/system/SmokeTestGame.cpp:30  
    #3 0x5565d5caa22a in host_server_invoker /home/oleg/devel/freeorion/test/system/SmokeTestGame.cpp:30  
  
SUMMARY: AddressSanitizer: stack-buffer-overflow /usr/include/boost/python/converter/builtin_converters.hpp:113 in boost::python::to_python_value<bool const&>::operator()(bool const&) const  
Shadow bytes around the buggy address:  
  0x0fe9b8b57430: f2 f2 00 f2 f2 f2 00 f2 f2 f2 00 f2 f2 f2 00 f2  
  0x0fe9b8b57440: f2 f2 00 f2 f2 f2 00 f2 f2 f2 00 f2 f2 f2 00 f2  
  0x0fe9b8b57450: f2 f2 00 f2 f2 f2 00 f2 f2 f2 00 f2 f2 f2 00 00  
  0x0fe9b8b57460: f2 f2 00 00 f2 f2 00 00 f2 f2 00 00 f2 f2 00 00  
  0x0fe9b8b57470: f2 f2 00 00 f2 f2 00 00 f2 f2 00 00 f2 f2 00 00  
=>0x0fe9b8b57480: f2 f2 00 00 f2 f2 00 00 00 f2[f2]f2 f2 f2 00 00  
  0x0fe9b8b57490: 00 f2 f2 f2 f2 f2 00 00 00 f2 f2 f2 f2 f2 00 00  
  0x0fe9b8b574a0: 00 f2 f2 f2 f2 f2 00 00 00 f3 f3 f3 f3 f3 00 00  
  0x0fe9b8b574b0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
  0x0fe9b8b574c0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
  0x0fe9b8b574d0: 00 00 00 00 f1 f1 f1 f1 f8 f2 f8 f2 f8 f2 f8 f2  
Shadow byte legend (one shadow byte represents 8 application bytes):  
  Addressable:           00  
  Partially addressable: 01 02 03 04 05 06 07   
  Heap left redzone:       fa  
  Freed heap region:       fd  
  Stack left redzone:      f1  
  Stack mid redzone:       f2  
  Stack right redzone:     f3  
  Stack after return:      f5  
  Stack use after scope:   f8  
  Global redzone:          f9  
  Global init order:       f6  
  Poisoned by user:        f7  
  Container overflow:      fc  
  Array cookie:            ac  
  Intra object redzone:    bb  
  ASan internal:           fe  
  Left alloca redzone:     ca  
  Right alloca redzone:    cb  
==32076==ABORTING  
```  
  
Encountered in https://github.com/freeorion/freeorion with tag `v0.5-rc1`  To reproduce build it with `-DBUILD_TESTING=On -DBUILD_CLIENT_GG=Off  -DBUILD_SERVER=Off -DBUILD_AI=Off` and run `ASAN_OPTIONS=detect_leaks=0 ./fo_unittest_parse --run_test=TestPythonParser/parse_techs_full`  
  
Mininified example https://github.com/o01eg/freeorion with branch `test-python-sanitize-mini` on CI: https://github.com/o01eg/freeorion/actions/runs/4264388156/jobs/7422403144

---

## Comment 1

> Username: o01eg  
> Created at: 2023-02-21 12:32:21 UTC  
> Updated at: 2023-02-21 12:53:41 UTC  
> Url: https://github.com/boostorg/python/issues/413#issuecomment-1438403486  

```  
(gdb) bt full  
...  
#8  0x00007ffff78c23fc in __asan::__asan_report_load1 (addr=<optimized out>) at ../../../../src/libsanitizer/asan/asan_rtl.cpp:120  
        bp = 140737168513488  
        pc = <optimized out>  
        local_stack = 140737353891072  
        sp = 140737168513480  
#9  0x00007ffff38eaee4 in boost::python::to_python_value<bool const&>::operator() (this=0x7fffecef9a80, x=@0x7fffecefd2d0: 22)  
    at /usr/include/boost/python/converter/builtin_converters.hpp:113  
No locals.  
#10 0x00007ffff3948e9e in boost::python::detail::invoke<boost::python::to_python_value<bool const&>, boost::python::detail::datum<bool const> > (rc=..., f=...)  
    at /usr/include/boost/python/detail/invoke.hpp:73  
No locals.  
#11 0x00007ffff39423f2 in boost::python::detail::caller_arity<0u>::impl<boost::python::detail::datum<bool const>, boost::python::return_value_policy<boost::python::return_by_value, boost::python::default_call_policies>, boost::mpl::vector1<bool const&> >::operator() (this=0x602000014898, args_=0x7ffff1544db8 <_PyRuntime+58904>)  
    at /usr/include/boost/python/detail/caller.hpp:233  
        inner_args = 0x7ffff1544db8 <_PyRuntime+58904>  
        result = 0x7fffedcf3540  
#12 0x00007ffff39405ef in boost::python::objects::caller_py_function_impl<boost::python::detail::caller<boost::python::detail::datum<bool const>, boost::python::return_value_policy<boost::python::return_by_value, boost::python::default_call_policies>, boost::mpl::vector1<bool const&> > >::operator() (this=0x602000014890, args=0x7ffff1544db8 <_PyRuntime+58904>, kw=0x0)  
    at /usr/include/boost/python/object/py_function.hpp:38  
No locals.  
#13 0x00007ffff450235d in boost::python::objects::py_function::operator() (kw=0x0, args=0x7ffff1544db8 <_PyRuntime+58904>, this=0x607000004a90) at ./boost/python/object/py_function.hpp:147  
No locals.  
#14 boost::python::objects::function::call (this=0x607000004a80, args=0x7ffff1544db8 <_PyRuntime+58904>, keywords=0x0) at libs/python/src/object/function.cpp:221  
        inner_args = {m_p = 0x7ffff1544db8 <_PyRuntime+58904>}  
        result = <optimized out>  
        min_arity = <optimized out>  
        max_arity = 0  
        n_unnamed_actual = 0  
        n_keyword_actual = 0  
        n_actual = 0  
        f = 0x607000004a80  
#15 0x00007ffff4502528 in boost::python::objects::(anonymous namespace)::bind_return::operator() (this=<optimized out>) at libs/python/src/object/function.cpp:581  
No locals.  
#16 boost::detail::function::void_function_ref_invoker0<boost::python::objects::(anonymous namespace)::bind_return, void>::invoke (function_obj_ptr=...)  
    at ./boost/function/function_template.hpp:193  
        f = <optimized out>  
#17 0x00007ffff450749b in boost::function0<void>::operator() (this=<optimized out>) at ./boost/function/function_template.hpp:763  
No locals.  
#18 boost::python::detail::exception_handler::operator() (this=<optimized out>, f=...) at libs/python/src/errors.cpp:74  
No locals.  
#19 0x00007ffff38e7a2e in boost::python::detail::translate_exception<(anonymous namespace)::import_error, void (*)((anonymous namespace)::import_error const&)>::operator() (  
    this=0x604000011118, handler=..., f=..., translate=0x7ffff38d4a85 <(anonymous namespace)::translate((anonymous namespace)::import_error const&)>)  
    at /usr/include/boost/python/detail/translate_exception.hpp:46  
No locals.  
#20 0x00007ffff38e7413 in boost::_bi::list3<boost::arg<1>, boost::arg<2>, boost::_bi::value<void (*)((anonymous namespace)::import_error const&)> >::operator()<bool, boost::python::detail::translate_exception<(anonymous namespace)::import_error, void (*)((anonymous namespace)::import_error const&)>, boost::_bi::rrlist2<boost::python::detail::exception_handler const&, boost::function0<void> const&> > (this=0x604000011120, f=..., a=...) at /usr/include/boost/bind/bind.hpp:388  
No locals.  
#21 0x00007ffff38e6a05 in boost::_bi::bind_t<bool, boost::python::detail::translate_exception<(anonymous namespace)::import_error, void (*)((anonymous namespace)::import_error const&)>, boost::_bi::list3<boost::arg<1>, boost::arg<2>, boost::_bi::value<void (*)((anonymous namespace)::import_error const&)> > >::operator()<boost::python::detail::exception_handler const&, boost::function0<void> const&> (this=0x604000011118, a1=..., a2=...) at /usr/include/boost/bind/bind.hpp:1318  
        a = {a1_ = @0x604000011110, a2_ = @0x7fffecef9e20}  
#22 0x00007ffff38e62c6 in boost::detail::function::function_obj_invoker2<boost::_bi::bind_t<bool, boost::python::detail::translate_exception<(anonymous namespace)::import_error, void (*)((anonymous namespace)::import_error const&)>, boost::_bi::list3<boost::arg<1>, boost::arg<2>, boost::_bi::value<void (*)((anonymous namespace)::import_error const&)> > >, bool, boost::python::detail::exception_handler const&, boost::function0<void> const&>::invoke (function_obj_ptr=..., a0=..., a1=...) at /usr/include/boost/function/function_template.hpp:137  
        f = 0x604000011118  
#23 0x00007ffff45073ad in boost::function2<bool, boost::python::detail::exception_handler const&, boost::function0<void> const&>::operator() (a1=..., a0=..., this=<optimized out>)  
    at ./boost/function/function_template.hpp:763  
No locals.  
#24 boost::python::detail::exception_handler::handle (f=..., this=<optimized out>) at ./boost/python/detail/exception_handler.hpp:41  
No locals.  
#25 boost::python::handle_exception_impl (f=...) at libs/python/src/errors.cpp:24  
No locals.  
#26 0x00007ffff44ffec2 in boost::python::handle_exception<boost::python::objects::(anonymous namespace)::bind_return> (f=...) at ./boost/python/errors.hpp:29  
No locals.  
#27 boost::python::objects::function_call (func=<optimized out>, args=<optimized out>, kw=<optimized out>) at libs/python/src/object/function.cpp:622  
        result = 0x0  
#28 0x00007ffff0f7baed in _PyObject_MakeTpCall () from /usr/lib/x86_64-linux-gnu/libpython3.11.so.1.0  
No symbol table info available.  
#29 0x00007ffff0f7d569 in ?? () from /usr/lib/x86_64-linux-gnu/libpython3.11.so.1.0  
No symbol table info available.  
#30 0x00007ffff0f7d68e in PyObject_CallFunction () from /usr/lib/x86_64-linux-gnu/libpython3.11.so.1.0  
No symbol table info available.  
#31 0x00007ffff0fd0439 in _PyObject_GenericGetAttrWithDict () from /usr/lib/x86_64-linux-gnu/libpython3.11.so.1.0  
No symbol table info available.  
#32 0x00007ffff0fcf978 in PyObject_GetAttr () from /usr/lib/x86_64-linux-gnu/libpython3.11.so.1.0  
No symbol table info available.  
#33 0x00007ffff0f0682c in _PyEval_EvalFrameDefault () from /usr/lib/x86_64-linux-gnu/libpython3.11.so.1.0  
No symbol table info available.  
#34 0x00007ffff106a6da in ?? () from /usr/lib/x86_64-linux-gnu/libpython3.11.so.1.0  
No symbol table info available.  
#35 0x00007ffff0f7e1cf in ?? () from /usr/lib/x86_64-linux-gnu/libpython3.11.so.1.0  
No symbol table info available.  
#36 0x00007ffff0f7e3ea in PyObject_CallMethodObjArgs () from /usr/lib/x86_64-linux-gnu/libpython3.11.so.1.0  
No symbol table info available.  
#37 0x00007ffff109637f in PyImport_ImportModuleLevelObject () from /usr/lib/x86_64-linux-gnu/libpython3.11.so.1.0  
No symbol table info available.  
#38 0x00007ffff106185e in ?? () from /usr/lib/x86_64-linux-gnu/libpython3.11.so.1.0  
No symbol table info available.  
#39 0x00007ffff0fc9c52 in ?? () from /usr/lib/x86_64-linux-gnu/libpython3.11.so.1.0  
No symbol table info available.  
#40 0x00007ffff0f0a7d3 in _PyEval_EvalFrameDefault () from /usr/lib/x86_64-linux-gnu/libpython3.11.so.1.0  
No symbol table info available.  
#41 0x00007ffff106a6da in ?? () from /usr/lib/x86_64-linux-gnu/libpython3.11.so.1.0  
No symbol table info available.  
#42 0x00007ffff0f7e1cf in ?? () from /usr/lib/x86_64-linux-gnu/libpython3.11.so.1.0  
No symbol table info available.  
#43 0x00007ffff0f7e3ea in PyObject_CallMethodObjArgs () from /usr/lib/x86_64-linux-gnu/libpython3.11.so.1.0  
No symbol table info available.  
#44 0x00007ffff109637f in PyImport_ImportModuleLevelObject () from /usr/lib/x86_64-linux-gnu/libpython3.11.so.1.0  
No symbol table info available.  
#45 0x00007ffff0f13378 in _PyEval_EvalFrameDefault () from /usr/lib/x86_64-linux-gnu/libpython3.11.so.1.0  
No symbol table info available.  
#46 0x00007ffff11cc73c in ?? () from /usr/lib/x86_64-linux-gnu/libpython3.11.so.1.0  
No symbol table info available.  
#47 0x00007ffff1068421 in PyEval_EvalCode () from /usr/lib/x86_64-linux-gnu/libpython3.11.so.1.0  
No symbol table info available.  
#48 0x00007ffff10b6d89 in ?? () from /usr/lib/x86_64-linux-gnu/libpython3.11.so.1.0  
No symbol table info available.  
#49 0x00007ffff10b7f3b in PyRun_StringFlags () from /usr/lib/x86_64-linux-gnu/libpython3.11.so.1.0  
No symbol table info available.  
#50 0x00007ffff450ae09 in boost::python::exec (  
    string=0x61d000142880 "from common.priorities import (\n    AFTER_ALL_TARGET_MAX_METERS_PRIORITY,\n    METER_OVERRIDE_PRIORITY,\n    TARGET_AFTER_2ND_SCALING_PRIORITY,\n)\n\nTech(\n    name=\"CON_OUTPOST\",\n    description=\"CON_OUTP"..., global=..., local=...) at ./boost/python/object_core.hpp:440  
        s = 0x61d000142880 "from common.priorities import (\n    AFTER_ALL_TARGET_MAX_METERS_PRIORITY,\n    METER_OVERRIDE_PRIORITY,\n    TARGET_AFTER_2ND_SCALING_PRIORITY,\n)\n\nTech(\n    name=\"CON_OUTPOST\",\n    description=\"CON_OUTP"...  
        result = <optimized out>  
#51 0x00007ffff38de221 in PythonParser::ParseFileCommon (this=0x7fffecefec50, path=..., globals=...,   
    filename="/home/oleg/devel/build-freeorion-rel/default/scripting/techs/construction/OUTPOST.focs.py",   
    file_contents="from common.priorities import (\n    AFTER_ALL_TARGET_MAX_METERS_PRIORITY,\n    METER_OVERRIDE_PRIORITY,\n    TARGET_AFTER_2ND_SCALING_PRIORITY,\n)\n\nTech(\n    name=\"CON_OUTPOST\",\n    description=\"CON_OUTP"...) at /home/oleg/devel/freeorion/parse/PythonParser.cpp:326  
        read_success = true  
...  
```

---

## Comment 2

> Username: o01eg  
> Created at: 2025-03-17 19:01:37 UTC  
> Url: https://github.com/boostorg/python/issues/413#issuecomment-2730552688  

```cpp  
(gdb) bt full  
...  
#9  0x00007ffff78ff41c in __asan::__asan_report_load1 (addr=<optimized out>) at ../../../../src/libsanitizer/asan/asan_rtl.cpp:128  
        bp = 140737488302608  
        pc = <optimized out>  
        local_stack = 140737354130192  
        sp = 140737488302600  
#10 0x0000555557676224 in boost::python::to_python_value<bool const&>::operator() (this=0x7fffeb29e0b0, x=@0x7fffeb836080: false)  
    at /usr/include/boost/python/converter/builtin_converters.hpp:113  
No locals.  
#11 0x00005555576dfc4c in boost::python::detail::invoke<boost::python::to_python_value<bool const&>, boost::python::detail::datum<bool const> > (rc=..., f=...)  
    at /usr/include/boost/python/detail/invoke.hpp:73  
No locals.  
#12 0x00005555576d95cc in boost::python::detail::caller_arity<0u>::impl<boost::python::detail::datum<bool const>, boost::python::return_value_policy<boost::python::return_by_value, boost::python::default_call_policies>, boost::mpl::vector1<bool const&> >::operator() (this=0x50200001acb8, args_=0x7ffff04f1cc8 <_PyRuntime+88296>)  
    at /usr/include/boost/python/detail/caller.hpp:233  
        inner_args = 0x7ffff04f1cc8 <_PyRuntime+88296>  
        result = 0x7fffec7faab0  
#13 0x00005555576d769d in boost::python::objects::caller_py_function_impl<boost::python::detail::caller<boost::python::detail::datum<bool const>, boost::python::return_value_policy<boost::python::return_by_value, boost::python::default_call_policies>, boost::mpl::vector1<bool const&> > >::operator() (this=0x50200001acb0, args=0x7ffff04f1cc8 <_PyRuntime+88296>, kw=0x0)  
    at /usr/include/boost/python/object/py_function.hpp:38  
No locals.  
#14 0x00007ffff06c73ee in boost::python::objects::py_function::operator() (this=0x507000013190, args=0x7ffff04f1cc8 <_PyRuntime+88296>, kw=<optimized out>)  
    at ./boost/python/object/py_function.hpp:147  
No locals.  
#15 boost::python::objects::function::call (this=0x507000013180, args=0x7ffff04f1cc8 <_PyRuntime+88296>, keywords=0x0) at libs/python/src/object/function.cpp:221  
        inner_args = <optimized out>  
        result = <optimized out>  
        min_arity = <optimized out>  
        max_arity = <optimized out>  
        n_unnamed_actual = 0  
        n_keyword_actual = 0  
        n_actual = 0  
        f = 0x507000013180  
#16 0x00007ffff06c766c in boost::python::objects::(anonymous namespace)::bind_return::operator() (this=<optimized out>) at libs/python/src/object/function.cpp:581  
No locals.  
#17 boost::detail::function::void_function_ref_invoker0<boost::python::objects::(anonymous namespace)::bind_return, void>::invoke (function_obj_ptr=...)  
    at ./boost/function/function_template.hpp:193  
        f = <optimized out>  
#18 0x00007ffff06cc69b in boost::function0<void>::operator() (this=<optimized out>) at ./boost/function/function_template.hpp:771  
No locals.  
#19 boost::python::detail::exception_handler::operator() (this=<optimized out>, f=...) at libs/python/src/errors.cpp:74  
No locals.  
#20 0x0000555557672e76 in boost::python::detail::translate_exception<(anonymous namespace)::import_error, void (*)((anonymous namespace)::import_error const&)>::operator() (  
    this=0x50400001c298, handler=..., f=..., translate=0x55555765ea23 <(anonymous namespace)::translate((anonymous namespace)::import_error const&)>)  
    at /usr/include/boost/python/detail/translate_exception.hpp:46  
No locals.  
#21 0x00005555576729c7 in boost::_bi::list3<boost::arg<1>, boost::arg<2>, boost::_bi::value<void (*)((anonymous namespace)::import_error const&)> >::operator()<bool, boost::python::detail::translate_exception<(anonymous namespace)::import_error, void (*)((anonymous namespace)::import_error const&)>, boost::_bi::rrlist2<boost::python::detail::exception_handler const&, boost::function0<void> const&> > (this=0x50400001c2a0, f=..., a=...) at /usr/include/boost/bind/bind.hpp:368  
No locals.  
#22 0x0000555557671fe9 in boost::_bi::bind_t<bool, boost::python::detail::translate_exception<(anonymous namespace)::import_error, void (*)((anonymous namespace)::import_error const&)>, boost::_bi::list3<boost::arg<1>, boost::arg<2>, boost::_bi::value<void (*)((anonymous namespace)::import_error const&)> > >::operator()<boost::python::detail::exception_handler const&, boost::function0<void> const&> (this=0x50400001c298, a1=..., a2=...) at /usr/include/boost/bind/bind.hpp:1298  
        a = {a1_ = @0x50400001c290, a2_ = @0x7fffffff3930}  
#23 0x000055555767177d in boost::detail::function::function_obj_invoker2<boost::_bi::bind_t<bool, boost::python::detail::translate_exception<(anonymous namespace)::import_error, void (*)((anonymous namespace)::import_error const&)>, boost::_bi::list3<boost::arg<1>, boost::arg<2>, boost::_bi::value<void (*)((anonymous namespace)::import_error const&)> > >, bool, boost::python::detail::exception_handler const&, boost::function0<void> const&>::invoke (function_obj_ptr=..., a0=..., a1=...) at /usr/include/boost/function/function_template.hpp:137  
        f = 0x50400001c298  
#24 0x0000555557672e76 in boost::python::detail::translate_exception<(anonymous namespace)::import_error, void (*)((anonymous namespace)::import_error const&)>::operator() (  
    this=0x504000018818, handler=..., f=..., translate=0x55555765ea23 <(anonymous namespace)::translate((anonymous namespace)::import_error const&)>)  
    at /usr/include/boost/python/detail/translate_exception.hpp:46  
No locals.  
#25 0x00005555576729c7 in boost::_bi::list3<boost::arg<1>, boost::arg<2>, boost::_bi::value<void (*)((anonymous namespace)::import_error const&)> >::operator()<bool, boost::python::detail::translate_exception<(anonymous namespace)::import_error, void (*)((anonymous namespace)::import_error const&)>, boost::_bi::rrlist2<boost::python::detail::exception_handler const&, boost::function0<void> const&> > (this=0x504000018820, f=..., a=...) at /usr/include/boost/bind/bind.hpp:368  
No locals.  
#26 0x0000555557671fe9 in boost::_bi::bind_t<bool, boost::python::detail::translate_exception<(anonymous namespace)::import_error, void (*)((anonymous namespace)::import_error const&)>, boost::_bi::list3<boost::arg<1>, boost::arg<2>, boost::_bi::value<void (*)((anonymous namespace)::import_error const&)> > >::operator()<boost::python::detail::exception_handler const&, boost::function0<void> const&> (this=0x504000018818, a1=..., a2=...) at /usr/include/boost/bind/bind.hpp:1298  
        a = {a1_ = @0x504000018810, a2_ = @0x7fffffff3930}  
#27 0x000055555767177d in boost::detail::function::function_obj_invoker2<boost::_bi::bind_t<bool, boost::python::detail::translate_exception<(anonymous namespace)::import_error, void (*)((anonymous namespace)::import_error const&)>, boost::_bi::list3<boost::arg<1>, boost::arg<2>, boost::_bi::value<void (*)((anonymous namespace)::import_error const&)> > >, bool, boost::python::detail::exception_handler const&, boost::function0<void> const&>::invoke (function_obj_ptr=..., a0=..., a1=...) at /usr/include/boost/function/function_template.hpp:137  
        f = 0x504000018818  
#28 0x0000555557672e76 in boost::python::detail::translate_exception<(anonymous namespace)::import_error, void (*)((anonymous namespace)::import_error const&)>::operator() (  
    this=0x504000013818, handler=..., f=..., translate=0x55555765ea23 <(anonymous namespace)::translate((anonymous namespace)::import_error const&)>)  
    at /usr/include/boost/python/detail/translate_exception.hpp:46  
No locals.  
#29 0x00005555576729c7 in boost::_bi::list3<boost::arg<1>, boost::arg<2>, boost::_bi::value<void (*)((anonymous namespace)::import_error const&)> >::operator()<bool, boost::python::detail::translate_exception<(anonymous namespace)::import_error, void (*)((anonymous namespace)::import_error const&)>, boost::_bi::rrlist2<boost::python::detail::exception_handler const&, boost::function0<void> const&> > (this=0x504000013820, f=..., a=...) at /usr/include/boost/bind/bind.hpp:368  
No locals.  
#30 0x0000555557671fe9 in boost::_bi::bind_t<bool, boost::python::detail::translate_exception<(anonymous namespace)::import_error, void (*)((anonymous namespace)::import_error const&)>, boost::_bi::list3<boost::arg<1>, boost::arg<2>, boost::_bi::value<void (*)((anonymous namespace)::import_error const&)> > >::operator()<boost::python::detail::exception_handler const&, boost::function0<void> const&> (this=0x504000013818, a1=..., a2=...) at /usr/include/boost/bind/bind.hpp:1298  
        a = {a1_ = @0x504000013810, a2_ = @0x7fffffff3930}  
#31 0x000055555767177d in boost::detail::function::function_obj_invoker2<boost::_bi::bind_t<bool, boost::python::detail::translate_exception<(anonymous namespace)::import_error, void (*)((anonymous namespace)::import_error const&)>, boost::_bi::list3<boost::arg<1>, boost::arg<2>, boost::_bi::value<void (*)((anonymous namespace)::import_error const&)> > >, bool, boost::python::detail::exception_handler const&, boost::function0<void> const&>::invoke (function_obj_ptr=..., a0=..., a1=...) at /usr/include/boost/function/function_template.hpp:137  
        f = 0x504000013818  
#32 0x00007ffff06cc551 in boost::function2<bool, boost::python::detail::exception_handler const&, boost::function0<void> const&>::operator() (this=<optimized out>, a0=..., a1=...)  
    at ./boost/function/function_template.hpp:771  
No locals.  
#33 boost::python::detail::exception_handler::handle (this=<optimized out>, f=...) at ./boost/python/detail/exception_handler.hpp:41  
No locals.  
#34 boost::python::handle_exception_impl (f=...) at libs/python/src/errors.cpp:24  
No locals.  
#35 0x00007ffff06c4406 in boost::python::handle_exception<boost::python::objects::(anonymous namespace)::bind_return> (f=...) at ./boost/python/errors.hpp:29  
No locals.  
#36 boost::python::objects::function_call (func=<optimized out>, args=<optimized out>, kw=<optimized out>) at libs/python/src/object/function.cpp:622  
        result = 0x0  
#37 0x00007fffeff0f09a in _PyObject_MakeTpCall (tstate=tstate@entry=0x7fffeb0f6040, callable=callable@entry=0x507000013180, args=0x7ffff04f1ce0 <_PyRuntime+88320>, nargs=<optimized out>,   
    keywords=keywords@entry=0x0) at ../Objects/call.c:242  
        call = <optimized out>  
        argstuple = <optimized out>  
        kwdict = 0x0  
        result = 0x0  
#38 0x00007fffeff0f6c1 in _PyObject_VectorcallTstate (tstate=0x7fffeb0f6040, callable=0x507000013180, args=<optimized out>, nargsf=<optimized out>, kwnames=0x0)  
    at ../Include/internal/pycore_call.h:166  
        nargs = <optimized out>  
        func = <optimized out>  
        res = <optimized out>  
#39 _PyObject_VectorcallTstate (tstate=0x7fffeb0f6040, callable=0x507000013180, args=<optimized out>, nargsf=<optimized out>, kwnames=0x0) at ../Include/internal/pycore_call.h:153  
        func = <optimized out>  
        res = <optimized out>  
        func = <optimized out>  
        res = <optimized out>  
        nargs = <optimized out>  
#40 _PyObject_CallFunctionVa (tstate=0x7fffeb0f6040, callable=callable@entry=0x507000013180, format=format@entry=0x7ffff06d8014 "()", va=va@entry=0x7fffffff3a30) at ../Objects/call.c:546  
        args = <optimized out>  
        small_stack = {0x7ffff04f1cc8 <_PyRuntime+88296>, 0x7ffff0490830, 0x7ffff37c3758, 0x7fffec96f940, 0x7fffec55c770}  
        small_stack_len = 5  
        stack = 0x7fffffff39d0  
        nargs = 1  
        i = <optimized out>  
        result = <optimized out>  
#41 0x00007fffeff0f799 in PyObject_CallFunction (callable=0x507000013180, format=0x7ffff06d8014 "()") at ../Objects/call.c:574  
        va = {{gp_offset = 16, fp_offset = 48, overflow_arg_area = 0x7fffffff3b20, reg_save_area = 0x7fffffff3a50}}  
        result = <optimized out>  
        tstate = <optimized out>  
#42 0x00007fffeff71a5e in _PyObject_GenericGetAttrWithDict (obj=0x7fffec96f940, name=0x7ffff04ea5b8 <_PyRuntime+57816>, dict=0x0, suppress=0) at ../Include/object.h:336  
        tp = 0x5190001d3890  
        descr = 0x7fffec55c770  
        res = 0x0  
        f = 0x7ffff06c1d80 <boost::python::static_data_descr_get(PyObject*, PyObject*, PyObject*)>  
#43 0x00007fffeff6ee0d in PyObject_GetAttr (v=0x7fffec96f940, name=0x7ffff04ea5b8 <_PyRuntime+57816>) at ../Objects/object.c:1261  
        tp = 0x5190001d3890  
        result = 0x0  
#44 0x00007ffff0042a85 in _PyEval_EvalFrameDefault (tstate=<optimized out>, frame=<optimized out>, throwflag=<optimized out>) at ../Python/generated_cases.c.h:3766  
        name = <optimized out>  
        op = <optimized out>  
        dealloc = <optimized out>  
        op = <optimized out>  
        dealloc = <optimized out>  
        this_instr = <optimized out>  
        owner = 0x7fffec96f940  
        attr = 0x7fffec782780  
        self_or_null = 0x0  
        opcode = <optimized out>  
        oparg = <optimized out>  
        entry_frame = {f_executable = 0x7ffff04a5340 <_Py_NoneStruct>, previous = 0x7fffefcf4200, f_funcobj = 0x2f37c3758, f_globals = 0x2, f_builtins = 0x0, f_locals = 0x7fffffff3db0,   
          frame_obj = 0x7fffffff3db0, instr_ptr = 0x7ffff02818e2 <_Py_INTERPRETER_TRAMPOLINE_INSTRUCTIONS+2>, stacktop = 0, return_offset = 0, owner = 3 '\003', localsplus = {  
            0x7fffeb0f6040}}  
        next_instr = <optimized out>  
        stack_pointer = <optimized out>  
        exception_unwind = <optimized out>  
        dying = <optimized out>  
        __func__ = "_PyEval_EvalFrameDefault"  
        opcode_targets = {0x7ffff004ccd3 <_PyEval_EvalFrameDefault+51827>, 0x7ffff0047e94 <_PyEval_EvalFrameDefault+31796>, 0x7ffff00474d5 <_PyEval_EvalFrameDefault+29301>,   
          0x7ffff0049208 <_PyEval_EvalFrameDefault+36776>, 0x7ffff0044d27 <_PyEval_EvalFrameDefault+19143>, 0x7ffff0044dd6 <_PyEval_EvalFrameDefault+19318>,   
          0x7ffff004278b <_PyEval_EvalFrameDefault+9515>, 0x7ffff0042884 <_PyEval_EvalFrameDefault+9764>, 0x7ffff00432b6 <_PyEval_EvalFrameDefault+12374>,   
          0x7ffff0041286 <_PyEval_EvalFrameDefault+4134>, 0x7ffff00410c4 <_PyEval_EvalFrameDefault+3684>, 0x7ffff0041154 <_PyEval_EvalFrameDefault+3828>,   
          0x7ffff0041333 <_PyEval_EvalFrameDefault+4307>, 0x7ffff004138d <_PyEval_EvalFrameDefault+4397>, 0x7ffff0041460 <_PyEval_EvalFrameDefault+4608>,   
          0x7ffff00413cd <_PyEval_EvalFrameDefault+4461>, 0x7ffff0043039 <_PyEval_EvalFrameDefault+11737>, 0x7ffff004ccb8 <_PyEval_EvalFrameDefault+51800>,   
          0x7ffff00445fd <_PyEval_EvalFrameDefault+17309>, 0x7ffff00444a8 <_PyEval_EvalFrameDefault+16968>, 0x7ffff004290c <_PyEval_EvalFrameDefault+9900>,   
          0x7ffff00443a1 <_PyEval_EvalFrameDefault+16705>, 0x7ffff0044068 <_PyEval_EvalFrameDefault+15880>, 0x7ffff00430d7 <_PyEval_EvalFrameDefault+11895>,   
          0x7ffff0045ee9 <_PyEval_EvalFrameDefault+23689>, 0x7ffff00464a7 <_PyEval_EvalFrameDefault+25159>, 0x7ffff0046e8d <_PyEval_EvalFrameDefault+27693>,   
          0x7ffff0046d91 <_PyEval_EvalFrameDefault+27441>, 0x7ffff0046de1 <_PyEval_EvalFrameDefault+27521>, 0x7ffff0046e37 <_PyEval_EvalFrameDefault+27607>,   
          0x7ffff00469ac <_PyEval_EvalFrameDefault+26444>, 0x7ffff00469d1 <_PyEval_EvalFrameDefault+26481>, 0x7ffff0046c1c <_PyEval_EvalFrameDefault+27068>,   
          0x7ffff0046c66 <_PyEval_EvalFrameDefault+27142>, 0x7ffff0045eb0 <_PyEval_EvalFrameDefault+23632>, 0x7ffff00488d5 <_PyEval_EvalFrameDefault+34421>,   
          0x7ffff0049502 <_PyEval_EvalFrameDefault+37538>, 0x7ffff004714a <_PyEval_EvalFrameDefault+28394>, 0x7ffff00456a8 <_PyEval_EvalFrameDefault+21576>,   
          0x7ffff004576a <_PyEval_EvalFrameDefault+21770>, 0x7ffff00458a0 <_PyEval_EvalFrameDefault+22080>, 0x7ffff0047618 <_PyEval_EvalFrameDefault+29624>,   
          0x7ffff004767e <_PyEval_EvalFrameDefault+29726>, 0x7ffff00476e4 <_PyEval_EvalFrameDefault+29828>, 0x7ffff0046855 <_PyEval_EvalFrameDefault+26101>,   
          0x7ffff0047f61 <_PyEval_EvalFrameDefault+32001>, 0x7ffff0045d33 <_PyEval_EvalFrameDefault+23251>, 0x7ffff0045e47 <_PyEval_EvalFrameDefault+23527>,   
          0x7ffff004923b <_PyEval_EvalFrameDefault+36827>, 0x7ffff004958a <_PyEval_EvalFrameDefault+37674>, 0x7ffff004872a <_PyEval_EvalFrameDefault+33994>,   
          0x7ffff0048801 <_PyEval_EvalFrameDefault+34209>, 0x7ffff004941e <_PyEval_EvalFrameDefault+37310>, 0x7ffff0049487 <_PyEval_EvalFrameDefault+37415>,   
          0x7ffff00409f5 <_PyEval_EvalFrameDefault+1941>, 0x7ffff00475a2 <_PyEval_EvalFrameDefault+29506>, 0x7ffff0047950 <_PyEval_EvalFrameDefault+30448>,   
          0x7ffff0043844 <_PyEval_EvalFrameDefault+13796>, 0x7ffff0043e56 <_PyEval_EvalFrameDefault+15350>, 0x7ffff0043a98 <_PyEval_EvalFrameDefault+14392>,   
          0x7ffff00437d4 <_PyEval_EvalFrameDefault+13684>, 0x7ffff0040e27 <_PyEval_EvalFrameDefault+3015>, 0x7ffff0040e75 <_PyEval_EvalFrameDefault+3093>,   
          0x7ffff0040ed3 <_PyEval_EvalFrameDefault+3187>, 0x7ffff0040f74 <_PyEval_EvalFrameDefault+3348>, 0x7ffff0040fcc <_PyEval_EvalFrameDefault+3436>,   
          0x7ffff0041025 <_PyEval_EvalFrameDefault+3525>, 0x7ffff0041074 <_PyEval_EvalFrameDefault+3604>, 0x7ffff004119e <_PyEval_EvalFrameDefault+3902>,   
          0x7ffff004121c <_PyEval_EvalFrameDefault+4028>, 0x7ffff004d14c <_PyEval_EvalFrameDefault+52972>, 0x7ffff004130b <_PyEval_EvalFrameDefault+4267>,   
          0x7ffff00414d9 <_PyEval_EvalFrameDefault+4729>, 0x7ffff0044426 <_PyEval_EvalFrameDefault+16838>, 0x7ffff0044207 <_PyEval_EvalFrameDefault+16295>,   
          0x7ffff0044262 <_PyEval_EvalFrameDefault+16386>, 0x7ffff0044095 <_PyEval_EvalFrameDefault+15925>, 0x7ffff0041721 <_PyEval_EvalFrameDefault+5313>,   
          0x7ffff0041761 <_PyEval_EvalFrameDefault+5377>, 0x7ffff004178d <_PyEval_EvalFrameDefault+5421>, 0x7ffff00417b5 <_PyEval_EvalFrameDefault+5461>,   
          0x7ffff0041811 <_PyEval_EvalFrameDefault+5553>, 0x7ffff0042dfb <_PyEval_EvalFrameDefault+11163>, 0x7ffff0045f56 <_PyEval_EvalFrameDefault+23798>,   
          0x7ffff0045fa3 <_PyEval_EvalFrameDefault+23875>, 0x7ffff0045ff6 <_PyEval_EvalFrameDefault+23958>, 0x7ffff004603c <_PyEval_EvalFrameDefault+24028>,   
          0x7ffff0046086 <_PyEval_EvalFrameDefault+24102>, 0x7ffff00460d5 <_PyEval_EvalFrameDefault+24181>, 0x7ffff004613e <_PyEval_EvalFrameDefault+24286>,   
          0x7ffff00461d8 <_PyEval_EvalFrameDefault+24440>, 0x7ffff004626f <_PyEval_EvalFrameDefault+24591>, 0x7ffff00464f2 <_PyEval_EvalFrameDefault+25234>,   
          0x7ffff0046575 <_PyEval_EvalFrameDefault+25365>, 0x7ffff0044cc6 <_PyEval_EvalFrameDefault+19046>, 0x7ffff0046f05 <_PyEval_EvalFrameDefault+27813>,   
          0x7ffff0046cd6 <_PyEval_EvalFrameDefault+27254>, 0x7ffff0046a3c <_PyEval_EvalFrameDefault+26588>, 0x7ffff0046a9c <_PyEval_EvalFrameDefault+26684>,   
          0x7ffff0046b2e <_PyEval_EvalFrameDefault+26830>, 0x7ffff0046bbc <_PyEval_EvalFrameDefault+26972>, 0x7ffff00494a1 <_PyEval_EvalFrameDefault+37441>,   
          0x7ffff0047a6e <_PyEval_EvalFrameDefault+30734>, 0x7ffff0047435 <_PyEval_EvalFrameDefault+29141>, 0x7ffff0046f5a <_PyEval_EvalFrameDefault+27898>,   
          0x7ffff00471c9 <_PyEval_EvalFrameDefault+28521>, 0x7ffff0047237 <_PyEval_EvalFrameDefault+28631>, 0x7ffff0047293 <_PyEval_EvalFrameDefault+28723>,   
          0x7ffff00450ee <_PyEval_EvalFrameDefault+20110>, 0x7ffff00453d3 <_PyEval_EvalFrameDefault+20851>, 0x7ffff004542f <_PyEval_EvalFrameDefault+20943>,  
          0x7ffff004548f <_PyEval_EvalFrameDefault+21039>, 0x7ffff0045509 <_PyEval_EvalFrameDefault+21161>, 0x7ffff00455a2 <_PyEval_EvalFrameDefault+21314>,   
          0x7ffff0045618 <_PyEval_EvalFrameDefault+21432>, 0x7ffff004585e <_PyEval_EvalFrameDefault+22014>, 0x7ffff0047727 <_PyEval_EvalFrameDefault+29895>,   
          0x7ffff0046645 <_PyEval_EvalFrameDefault+25573>, 0x7ffff0046914 <_PyEval_EvalFrameDefault+26292>, 0x7ffff0048d1c <_PyEval_EvalFrameDefault+35516> <repeats 30 times>,   
          0x7ffff00473f5 <_PyEval_EvalFrameDefault+29077>, 0x7ffff0047b0a <_PyEval_EvalFrameDefault+30890>, 0x7ffff0047cb0 <_PyEval_EvalFrameDefault+31312>,   
          0x7ffff0047da2 <_PyEval_EvalFrameDefault+31554>, 0x7ffff0048ebc <_PyEval_EvalFrameDefault+35932>, 0x7ffff0048f56 <_PyEval_EvalFrameDefault+36086>,   
          0x7ffff0049048 <_PyEval_EvalFrameDefault+36328>, 0x7ffff00490e2 <_PyEval_EvalFrameDefault+36482>, 0x7ffff0044f96 <_PyEval_EvalFrameDefault+19766>,   
          0x7ffff00489de <_PyEval_EvalFrameDefault+34686>, 0x7ffff0048a17 <_PyEval_EvalFrameDefault+34743>, 0x7ffff0048a9f <_PyEval_EvalFrameDefault+34879>,   
          0x7ffff0048bc8 <_PyEval_EvalFrameDefault+35176>, 0x7ffff00479f3 <_PyEval_EvalFrameDefault+30611>, 0x7ffff0047a3a <_PyEval_EvalFrameDefault+30682>,   
          0x7ffff00491d4 <_PyEval_EvalFrameDefault+36724>, 0x7ffff0046714 <_PyEval_EvalFrameDefault+25780>, 0x7ffff00484c8 <_PyEval_EvalFrameDefault+33384>,   
          0x7ffff00485f1 <_PyEval_EvalFrameDefault+33681>, 0x7ffff0047823 <_PyEval_EvalFrameDefault+30147>, 0x7ffff00492f6 <_PyEval_EvalFrameDefault+37014>,   
          0x7ffff0043855 <_PyEval_EvalFrameDefault+13813>, 0x7ffff004394e <_PyEval_EvalFrameDefault+14062>, 0x7ffff00446a4 <_PyEval_EvalFrameDefault+17476>,   
          0x7ffff00447e0 <_PyEval_EvalFrameDefault+17792>, 0x7ffff0044918 <_PyEval_EvalFrameDefault+18104>, 0x7ffff0044a48 <_PyEval_EvalFrameDefault+18408>,   
          0x7ffff004832a <_PyEval_EvalFrameDefault+32970>, 0x7ffff0042d1d <_PyEval_EvalFrameDefault+10941>, 0x7ffff0042530 <_PyEval_EvalFrameDefault+8912>,   
          0x7ffff0042d51 <_PyEval_EvalFrameDefault+10993>, 0x7ffff00420ec <_PyEval_EvalFrameDefault+7820>, 0x7ffff0042700 <_PyEval_EvalFrameDefault+9376>,   
          0x7ffff0043c2b <_PyEval_EvalFrameDefault+14795>, 0x7ffff0043e74 <_PyEval_EvalFrameDefault+15380>, 0x7ffff0042f22 <_PyEval_EvalFrameDefault+11458>,   
          0x7ffff0043992 <_PyEval_EvalFrameDefault+14130>, 0x7ffff0043b57 <_PyEval_EvalFrameDefault+14583>, 0x7ffff004162d <_PyEval_EvalFrameDefault+5069>,   
          0x7ffff004450e <_PyEval_EvalFrameDefault+17070>, 0x7ffff0043fe8 <_PyEval_EvalFrameDefault+15752>, 0x7ffff0044549 <_PyEval_EvalFrameDefault+17129>,   
          0x7ffff0043724 <_PyEval_EvalFrameDefault+13508>, 0x7ffff0042964 <_PyEval_EvalFrameDefault+9988>, 0x7ffff0042b14 <_PyEval_EvalFrameDefault+10420>,   
          0x7ffff0042bdb <_PyEval_EvalFrameDefault+10619>, 0x7ffff0042c1c <_PyEval_EvalFrameDefault+10684>, 0x7ffff0042c8b <_PyEval_EvalFrameDefault+10795>,   
          0x7ffff00481db <_PyEval_EvalFrameDefault+32635>, 0x7ffff00482b1 <_PyEval_EvalFrameDefault+32849>, 0x7ffff0047f7f <_PyEval_EvalFrameDefault+32031>,   
          0x7ffff0040597 <_PyEval_EvalFrameDefault+823>, 0x7ffff0048017 <_PyEval_EvalFrameDefault+32183>, 0x7ffff00480c8 <_PyEval_EvalFrameDefault+32360>,   
          0x7ffff004636b <_PyEval_EvalFrameDefault+24843>, 0x7ffff00463fc <_PyEval_EvalFrameDefault+24988>, 0x7ffff004658f <_PyEval_EvalFrameDefault+25391>,   
          0x7ffff0044ba0 <_PyEval_EvalFrameDefault+18752>, 0x7ffff00473bc <_PyEval_EvalFrameDefault+29020>, 0x7ffff0047086 <_PyEval_EvalFrameDefault+28198>,   
          0x7ffff0047301 <_PyEval_EvalFrameDefault+28833>, 0x7ffff0045051 <_PyEval_EvalFrameDefault+19953>, 0x7ffff0045294 <_PyEval_EvalFrameDefault+20532>,   
          0x7ffff0045a21 <_PyEval_EvalFrameDefault+22465>, 0x7ffff0045b51 <_PyEval_EvalFrameDefault+22769>, 0x7ffff0045aa3 <_PyEval_EvalFrameDefault+22595>,   
          0x7ffff0045b12 <_PyEval_EvalFrameDefault+22706>, 0x7ffff0045c15 <_PyEval_EvalFrameDefault+22965>, 0x7ffff00477b2 <_PyEval_EvalFrameDefault+30034>,   
          0x7ffff0045c8c <_PyEval_EvalFrameDefault+23084>, 0x7ffff0045cb7 <_PyEval_EvalFrameDefault+23127>...}  
        word = <optimized out>  
        word = <optimized out>  
#45 0x00007fffeff1023a in _PyObject_VectorcallTstate (tstate=0x7fffeb0f6040, callable=0x7fffec9b3600, args=0x7fffffff3db0, nargsf=2, kwnames=0x0) at ../Include/internal/pycore_call.h:168  
        func = <optimized out>  
        res = <optimized out>  
        func = <optimized out>  
        res = <optimized out>  
        nargs = <optimized out>  
#46 object_vacall (tstate=tstate@entry=0x7fffeb0f6040, base=base@entry=0x0, callable=0x7fffec9b3600, vargs=vargs@entry=0x7fffffff3e40) at ../Objects/call.c:819  
        small_stack = {0x7fffec56d2f0, 0x7fffec91d030, 0x7ffff0221817, 0x7ffff0490830, 0x7ffff37c3758}  
        stack = 0x7fffffff3db0  
        nargs = <optimized out>  
        result = <optimized out>  
        i = <optimized out>  
        countva = {{gp_offset = 40, fp_offset = 48, overflow_arg_area = 0x7fffffff3f30, reg_save_area = 0x7fffffff3e60}}  
#47 0x00007fffeff1043b in PyObject_CallMethodObjArgs (obj=0x0, name=0x7ffff04e7860 <_PyRuntime+46208>) at ../Objects/call.c:880  
        tstate = 0x7fffeb0f6040  
        callable = 0x7fffec9b3600  
        is_method = <optimized out>  
        vargs = {{gp_offset = 32, fp_offset = 48, overflow_arg_area = 0x7fffffff3f30, reg_save_area = 0x7fffffff3e60}}  
        result = <optimized out>  
#48 0x00007ffff0089e13 in import_find_and_load (tstate=0x7fffeb0f6040, abs_name=0x7fffec56d2f0) at ../Python/import.c:3692  
        interp = 0x7fffeb0c6800  
        import_time = <optimized out>  
        accumulated_copy = 0  
        sys_meta_path = 0x7fffec994f00  
        sys_path_hooks = <optimized out>  
        mod = 0x0  
        t1 = 0  
        sys_path = <optimized out>  
        mod = <optimized out>  
        interp = <optimized out>  
        import_time = <optimized out>  
        t1 = <optimized out>  
        accumulated_copy = <optimized out>  
        sys_path = <optimized out>  
        sys_meta_path = <optimized out>  
        sys_path_hooks = <optimized out>  
        t2 = <optimized out>  
        cum = <optimized out>  
#49 PyImport_ImportModuleLevelObject (name=0x7fffec56d2f0, globals=<optimized out>, locals=<optimized out>, fromlist=0x0, level=0) at ../Python/import.c:3774  
        tstate = 0x7fffeb0f6040  
        abs_name = 0x7fffec56d2f0  
        final_mod = 0x0  
        mod = <optimized out>  
        package = 0x0  
        interp = 0x7fffeb0c6800  
        has_from = <optimized out>  
        error = <optimized out>  
#50 0x00007fffeff6cc71 in cfunction_vectorcall_FASTCALL_KEYWORDS (func=0x7fffec91d030, args=0x7fffec56d2e8, nargsf=<optimized out>, kwnames=0x0) at ../Objects/methodobject.c:441  
        tstate = 0x7fffeb0f6040  
        nargs = 1  
        meth = <optimized out>  
        result = <optimized out>  
#51 0x00007ffff0040ae3 in _PyEval_EvalFrameDefault (tstate=<optimized out>, frame=<optimized out>, throwflag=<optimized out>) at ../Python/generated_cases.c.h:1355  
        func = 0x7fffec91d030  
        xop = <optimized out>  
        this_instr = 0x7fffec99a5c6  
        kwargs = 0x7fffec573f80  
        callargs = 0x7fffec56d2d0  
        result = <optimized out>  
        op = <optimized out>  
        dealloc = <optimized out>  
        opcode = <optimized out>  
        oparg = <optimized out>  
        entry_frame = {f_executable = 0x7ffff04a5340 <_Py_NoneStruct>, previous = 0x7fffefcf4020, f_funcobj = 0x2f37c3758, f_globals = 0x2, f_builtins = 0x0, f_locals = 0x7fffffff4200,   
          frame_obj = 0x7fffffff4200, instr_ptr = 0x7ffff02818e2 <_Py_INTERPRETER_TRAMPOLINE_INSTRUCTIONS+2>, stacktop = 0, return_offset = 0, owner = 3 '\003', localsplus = {  
            0x7fffeb0f6040}}  
        next_instr = 0x7fffec99a5c8  
        stack_pointer = <optimized out>  
        exception_unwind = <optimized out>  
        dying = <optimized out>  
        __func__ = "_PyEval_EvalFrameDefault"  
        opcode_targets = {0x7ffff004ccd3 <_PyEval_EvalFrameDefault+51827>, 0x7ffff0047e94 <_PyEval_EvalFrameDefault+31796>, 0x7ffff00474d5 <_PyEval_EvalFrameDefault+29301>,   
          0x7ffff0049208 <_PyEval_EvalFrameDefault+36776>, 0x7ffff0044d27 <_PyEval_EvalFrameDefault+19143>, 0x7ffff0044dd6 <_PyEval_EvalFrameDefault+19318>,   
          0x7ffff004278b <_PyEval_EvalFrameDefault+9515>, 0x7ffff0042884 <_PyEval_EvalFrameDefault+9764>, 0x7ffff00432b6 <_PyEval_EvalFrameDefault+12374>,   
          0x7ffff0041286 <_PyEval_EvalFrameDefault+4134>, 0x7ffff00410c4 <_PyEval_EvalFrameDefault+3684>, 0x7ffff0041154 <_PyEval_EvalFrameDefault+3828>,   
          0x7ffff0041333 <_PyEval_EvalFrameDefault+4307>, 0x7ffff004138d <_PyEval_EvalFrameDefault+4397>, 0x7ffff0041460 <_PyEval_EvalFrameDefault+4608>,   
          0x7ffff00413cd <_PyEval_EvalFrameDefault+4461>, 0x7ffff0043039 <_PyEval_EvalFrameDefault+11737>, 0x7ffff004ccb8 <_PyEval_EvalFrameDefault+51800>,   
          0x7ffff00445fd <_PyEval_EvalFrameDefault+17309>, 0x7ffff00444a8 <_PyEval_EvalFrameDefault+16968>, 0x7ffff004290c <_PyEval_EvalFrameDefault+9900>,   
          0x7ffff00443a1 <_PyEval_EvalFrameDefault+16705>, 0x7ffff0044068 <_PyEval_EvalFrameDefault+15880>, 0x7ffff00430d7 <_PyEval_EvalFrameDefault+11895>,   
          0x7ffff0045ee9 <_PyEval_EvalFrameDefault+23689>, 0x7ffff00464a7 <_PyEval_EvalFrameDefault+25159>, 0x7ffff0046e8d <_PyEval_EvalFrameDefault+27693>,   
          0x7ffff0046d91 <_PyEval_EvalFrameDefault+27441>, 0x7ffff0046de1 <_PyEval_EvalFrameDefault+27521>, 0x7ffff0046e37 <_PyEval_EvalFrameDefault+27607>,   
          0x7ffff00469ac <_PyEval_EvalFrameDefault+26444>, 0x7ffff00469d1 <_PyEval_EvalFrameDefault+26481>, 0x7ffff0046c1c <_PyEval_EvalFrameDefault+27068>,   
          0x7ffff0046c66 <_PyEval_EvalFrameDefault+27142>, 0x7ffff0045eb0 <_PyEval_EvalFrameDefault+23632>, 0x7ffff00488d5 <_PyEval_EvalFrameDefault+34421>,   
          0x7ffff0049502 <_PyEval_EvalFrameDefault+37538>, 0x7ffff004714a <_PyEval_EvalFrameDefault+28394>, 0x7ffff00456a8 <_PyEval_EvalFrameDefault+21576>,   
          0x7ffff004576a <_PyEval_EvalFrameDefault+21770>, 0x7ffff00458a0 <_PyEval_EvalFrameDefault+22080>, 0x7ffff0047618 <_PyEval_EvalFrameDefault+29624>,   
          0x7ffff004767e <_PyEval_EvalFrameDefault+29726>, 0x7ffff00476e4 <_PyEval_EvalFrameDefault+29828>, 0x7ffff0046855 <_PyEval_EvalFrameDefault+26101>,   
          0x7ffff0047f61 <_PyEval_EvalFrameDefault+32001>, 0x7ffff0045d33 <_PyEval_EvalFrameDefault+23251>, 0x7ffff0045e47 <_PyEval_EvalFrameDefault+23527>,   
          0x7ffff004923b <_PyEval_EvalFrameDefault+36827>, 0x7ffff004958a <_PyEval_EvalFrameDefault+37674>, 0x7ffff004872a <_PyEval_EvalFrameDefault+33994>,   
          0x7ffff0048801 <_PyEval_EvalFrameDefault+34209>, 0x7ffff004941e <_PyEval_EvalFrameDefault+37310>, 0x7ffff0049487 <_PyEval_EvalFrameDefault+37415>,   
          0x7ffff00409f5 <_PyEval_EvalFrameDefault+1941>, 0x7ffff00475a2 <_PyEval_EvalFrameDefault+29506>, 0x7ffff0047950 <_PyEval_EvalFrameDefault+30448>,   
          0x7ffff0043844 <_PyEval_EvalFrameDefault+13796>, 0x7ffff0043e56 <_PyEval_EvalFrameDefault+15350>, 0x7ffff0043a98 <_PyEval_EvalFrameDefault+14392>,   
          0x7ffff00437d4 <_PyEval_EvalFrameDefault+13684>, 0x7ffff0040e27 <_PyEval_EvalFrameDefault+3015>, 0x7ffff0040e75 <_PyEval_EvalFrameDefault+3093>,   
          0x7ffff0040ed3 <_PyEval_EvalFrameDefault+3187>, 0x7ffff0040f74 <_PyEval_EvalFrameDefault+3348>, 0x7ffff0040fcc <_PyEval_EvalFrameDefault+3436>,   
          0x7ffff0041025 <_PyEval_EvalFrameDefault+3525>, 0x7ffff0041074 <_PyEval_EvalFrameDefault+3604>, 0x7ffff004119e <_PyEval_EvalFrameDefault+3902>,   
          0x7ffff004121c <_PyEval_EvalFrameDefault+4028>, 0x7ffff004d14c <_PyEval_EvalFrameDefault+52972>, 0x7ffff004130b <_PyEval_EvalFrameDefault+4267>,   
          0x7ffff00414d9 <_PyEval_EvalFrameDefault+4729>, 0x7ffff0044426 <_PyEval_EvalFrameDefault+16838>, 0x7ffff0044207 <_PyEval_EvalFrameDefault+16295>,   
          0x7ffff0044262 <_PyEval_EvalFrameDefault+16386>, 0x7ffff0044095 <_PyEval_EvalFrameDefault+15925>, 0x7ffff0041721 <_PyEval_EvalFrameDefault+5313>,   
          0x7ffff0041761 <_PyEval_EvalFrameDefault+5377>, 0x7ffff004178d <_PyEval_EvalFrameDefault+5421>, 0x7ffff00417b5 <_PyEval_EvalFrameDefault+5461>,   
          0x7ffff0041811 <_PyEval_EvalFrameDefault+5553>, 0x7ffff0042dfb <_PyEval_EvalFrameDefault+11163>, 0x7ffff0045f56 <_PyEval_EvalFrameDefault+23798>,   
          0x7ffff0045fa3 <_PyEval_EvalFrameDefault+23875>, 0x7ffff0045ff6 <_PyEval_EvalFrameDefault+23958>, 0x7ffff004603c <_PyEval_EvalFrameDefault+24028>,   
          0x7ffff0046086 <_PyEval_EvalFrameDefault+24102>, 0x7ffff00460d5 <_PyEval_EvalFrameDefault+24181>, 0x7ffff004613e <_PyEval_EvalFrameDefault+24286>,   
          0x7ffff00461d8 <_PyEval_EvalFrameDefault+24440>, 0x7ffff004626f <_PyEval_EvalFrameDefault+24591>, 0x7ffff00464f2 <_PyEval_EvalFrameDefault+25234>,   
          0x7ffff0046575 <_PyEval_EvalFrameDefault+25365>, 0x7ffff0044cc6 <_PyEval_EvalFrameDefault+19046>, 0x7ffff0046f05 <_PyEval_EvalFrameDefault+27813>,   
          0x7ffff0046cd6 <_PyEval_EvalFrameDefault+27254>, 0x7ffff0046a3c <_PyEval_EvalFrameDefault+26588>, 0x7ffff0046a9c <_PyEval_EvalFrameDefault+26684>,   
          0x7ffff0046b2e <_PyEval_EvalFrameDefault+26830>, 0x7ffff0046bbc <_PyEval_EvalFrameDefault+26972>, 0x7ffff00494a1 <_PyEval_EvalFrameDefault+37441>,   
          0x7ffff0047a6e <_PyEval_EvalFrameDefault+30734>, 0x7ffff0047435 <_PyEval_EvalFrameDefault+29141>, 0x7ffff0046f5a <_PyEval_EvalFrameDefault+27898>,   
          0x7ffff00471c9 <_PyEval_EvalFrameDefault+28521>, 0x7ffff0047237 <_PyEval_EvalFrameDefault+28631>, 0x7ffff0047293 <_PyEval_EvalFrameDefault+28723>,   
          0x7ffff00450ee <_PyEval_EvalFrameDefault+20110>, 0x7ffff00453d3 <_PyEval_EvalFrameDefault+20851>, 0x7ffff004542f <_PyEval_EvalFrameDefault+20943>,   
          0x7ffff004548f <_PyEval_EvalFrameDefault+21039>, 0x7ffff0045509 <_PyEval_EvalFrameDefault+21161>, 0x7ffff00455a2 <_PyEval_EvalFrameDefault+21314>,   
          0x7ffff0045618 <_PyEval_EvalFrameDefault+21432>, 0x7ffff004585e <_PyEval_EvalFrameDefault+22014>, 0x7ffff0047727 <_PyEval_EvalFrameDefault+29895>,   
          0x7ffff0046645 <_PyEval_EvalFrameDefault+25573>, 0x7ffff0046914 <_PyEval_EvalFrameDefault+26292>, 0x7ffff0048d1c <_PyEval_EvalFrameDefault+35516> <repeats 30 times>,   
          0x7ffff00473f5 <_PyEval_EvalFrameDefault+29077>, 0x7ffff0047b0a <_PyEval_EvalFrameDefault+30890>, 0x7ffff0047cb0 <_PyEval_EvalFrameDefault+31312>,   
          0x7ffff0047da2 <_PyEval_EvalFrameDefault+31554>, 0x7ffff0048ebc <_PyEval_EvalFrameDefault+35932>, 0x7ffff0048f56 <_PyEval_EvalFrameDefault+36086>,   
          0x7ffff0049048 <_PyEval_EvalFrameDefault+36328>, 0x7ffff00490e2 <_PyEval_EvalFrameDefault+36482>, 0x7ffff0044f96 <_PyEval_EvalFrameDefault+19766>,   
          0x7ffff00489de <_PyEval_EvalFrameDefault+34686>, 0x7ffff0048a17 <_PyEval_EvalFrameDefault+34743>, 0x7ffff0048a9f <_PyEval_EvalFrameDefault+34879>,  
          0x7ffff0048bc8 <_PyEval_EvalFrameDefault+35176>, 0x7ffff00479f3 <_PyEval_EvalFrameDefault+30611>, 0x7ffff0047a3a <_PyEval_EvalFrameDefault+30682>,   
          0x7ffff00491d4 <_PyEval_EvalFrameDefault+36724>, 0x7ffff0046714 <_PyEval_EvalFrameDefault+25780>, 0x7ffff00484c8 <_PyEval_EvalFrameDefault+33384>,   
          0x7ffff00485f1 <_PyEval_EvalFrameDefault+33681>, 0x7ffff0047823 <_PyEval_EvalFrameDefault+30147>, 0x7ffff00492f6 <_PyEval_EvalFrameDefault+37014>,   
          0x7ffff0043855 <_PyEval_EvalFrameDefault+13813>, 0x7ffff004394e <_PyEval_EvalFrameDefault+14062>, 0x7ffff00446a4 <_PyEval_EvalFrameDefault+17476>,   
          0x7ffff00447e0 <_PyEval_EvalFrameDefault+17792>, 0x7ffff0044918 <_PyEval_EvalFrameDefault+18104>, 0x7ffff0044a48 <_PyEval_EvalFrameDefault+18408>,   
          0x7ffff004832a <_PyEval_EvalFrameDefault+32970>, 0x7ffff0042d1d <_PyEval_EvalFrameDefault+10941>, 0x7ffff0042530 <_PyEval_EvalFrameDefault+8912>,   
          0x7ffff0042d51 <_PyEval_EvalFrameDefault+10993>, 0x7ffff00420ec <_PyEval_EvalFrameDefault+7820>, 0x7ffff0042700 <_PyEval_EvalFrameDefault+9376>,   
          0x7ffff0043c2b <_PyEval_EvalFrameDefault+14795>, 0x7ffff0043e74 <_PyEval_EvalFrameDefault+15380>, 0x7ffff0042f22 <_PyEval_EvalFrameDefault+11458>,   
          0x7ffff0043992 <_PyEval_EvalFrameDefault+14130>, 0x7ffff0043b57 <_PyEval_EvalFrameDefault+14583>, 0x7ffff004162d <_PyEval_EvalFrameDefault+5069>,   
          0x7ffff004450e <_PyEval_EvalFrameDefault+17070>, 0x7ffff0043fe8 <_PyEval_EvalFrameDefault+15752>, 0x7ffff0044549 <_PyEval_EvalFrameDefault+17129>,   
          0x7ffff0043724 <_PyEval_EvalFrameDefault+13508>, 0x7ffff0042964 <_PyEval_EvalFrameDefault+9988>, 0x7ffff0042b14 <_PyEval_EvalFrameDefault+10420>,   
          0x7ffff0042bdb <_PyEval_EvalFrameDefault+10619>, 0x7ffff0042c1c <_PyEval_EvalFrameDefault+10684>, 0x7ffff0042c8b <_PyEval_EvalFrameDefault+10795>,   
          0x7ffff00481db <_PyEval_EvalFrameDefault+32635>, 0x7ffff00482b1 <_PyEval_EvalFrameDefault+32849>, 0x7ffff0047f7f <_PyEval_EvalFrameDefault+32031>,   
          0x7ffff0040597 <_PyEval_EvalFrameDefault+823>, 0x7ffff0048017 <_PyEval_EvalFrameDefault+32183>, 0x7ffff00480c8 <_PyEval_EvalFrameDefault+32360>,   
          0x7ffff004636b <_PyEval_EvalFrameDefault+24843>, 0x7ffff00463fc <_PyEval_EvalFrameDefault+24988>, 0x7ffff004658f <_PyEval_EvalFrameDefault+25391>,   
          0x7ffff0044ba0 <_PyEval_EvalFrameDefault+18752>, 0x7ffff00473bc <_PyEval_EvalFrameDefault+29020>, 0x7ffff0047086 <_PyEval_EvalFrameDefault+28198>,   
          0x7ffff0047301 <_PyEval_EvalFrameDefault+28833>, 0x7ffff0045051 <_PyEval_EvalFrameDefault+19953>, 0x7ffff0045294 <_PyEval_EvalFrameDefault+20532>,   
          0x7ffff0045a21 <_PyEval_EvalFrameDefault+22465>, 0x7ffff0045b51 <_PyEval_EvalFrameDefault+22769>, 0x7ffff0045aa3 <_PyEval_EvalFrameDefault+22595>,   
          0x7ffff0045b12 <_PyEval_EvalFrameDefault+22706>, 0x7ffff0045c15 <_PyEval_EvalFrameDefault+22965>, 0x7ffff00477b2 <_PyEval_EvalFrameDefault+30034>,   
          0x7ffff0045c8c <_PyEval_EvalFrameDefault+23084>, 0x7ffff0045cb7 <_PyEval_EvalFrameDefault+23127>...}  
        word = <optimized out>  
        word = <optimized out>  
#52 0x00007fffeff1023a in _PyObject_VectorcallTstate (tstate=0x7fffeb0f6040, callable=0x7fffec9b3600, args=0x7fffffff4200, nargsf=2, kwnames=0x0) at ../Include/internal/pycore_call.h:168  
        func = <optimized out>  
        res = <optimized out>  
        func = <optimized out>  
        res = <optimized out>  
        nargs = <optimized out>  
#53 object_vacall (tstate=tstate@entry=0x7fffeb0f6040, base=base@entry=0x0, callable=0x7fffec9b3600, vargs=vargs@entry=0x7fffffff4290) at ../Objects/call.c:819  
        small_stack = {0x7fffec570eb0, 0x7fffec91d030, 0x7ffff0221817, 0x7ffff0490830, 0x7ffff37c3758}  
        stack = 0x7fffffff4200  
        nargs = <optimized out>  
        result = <optimized out>  
        i = <optimized out>  
        countva = {{gp_offset = 40, fp_offset = 48, overflow_arg_area = 0x7fffffff4380, reg_save_area = 0x7fffffff42b0}}  
#54 0x00007fffeff1043b in PyObject_CallMethodObjArgs (obj=0x0, name=0x7ffff04e7860 <_PyRuntime+46208>) at ../Objects/call.c:880  
        tstate = 0x7fffeb0f6040  
        callable = 0x7fffec9b3600  
        is_method = <optimized out>  
        vargs = {{gp_offset = 32, fp_offset = 48, overflow_arg_area = 0x7fffffff4380, reg_save_area = 0x7fffffff42b0}}  
        result = <optimized out>  
#55 0x00007ffff0089e13 in import_find_and_load (tstate=0x7fffeb0f6040, abs_name=0x7fffec570eb0) at ../Python/import.c:3692  
        interp = 0x7fffeb0c6800  
        import_time = <optimized out>  
        accumulated_copy = 0  
        sys_meta_path = 0x7fffec994f00  
        sys_path_hooks = <optimized out>  
        mod = 0x0  
        t1 = 0  
        sys_path = <optimized out>  
        mod = <optimized out>  
        interp = <optimized out>  
        import_time = <optimized out>  
        t1 = <optimized out>  
        accumulated_copy = <optimized out>  
        sys_path = <optimized out>  
        sys_meta_path = <optimized out>  
        sys_path_hooks = <optimized out>  
        t2 = <optimized out>  
        cum = <optimized out>  
#56 PyImport_ImportModuleLevelObject (name=0x7fffec570eb0, globals=<optimized out>, locals=<optimized out>, fromlist=0x7fffec56c250, level=0) at ../Python/import.c:3774  
        tstate = 0x7fffeb0f6040  
        abs_name = 0x7fffec570eb0  
        final_mod = 0x0  
        mod = <optimized out>  
        package = 0x0  
        interp = 0x7fffeb0c6800  
        has_from = <optimized out>  
        error = <optimized out>  
#57 0x00007ffff004b4b2 in import_name (tstate=<optimized out>, frame=0x7fffefcf4020, name=0x7fffec570eb0, fromlist=0x7fffec56c250, level=0x7ffff04dfa90 <_PyRuntime+14000>)  
    at ../Python/ceval.c:2727  
        ilevel = <optimized out>  
        import_func = 0x7fffec91d030  
        locals = 0x7fffec570c40  
        args = {0x7fffefcf4068, 0x7fffec5682c0, 0x7ffff021a3e2, 0x7fffeb0f6040, 0x7ffff04a0f40 <PyFunction_Type>}  
        res = <optimized out>  
        import_func = <optimized out>  
        locals = <optimized out>  
        args = <optimized out>  
        res = <optimized out>  
        ilevel = <optimized out>  
        op = <optimized out>  
        dealloc = <optimized out>  
        op = <optimized out>  
        dealloc = <optimized out>  
#58 _PyEval_EvalFrameDefault (tstate=<optimized out>, frame=<optimized out>, throwflag=<optimized out>) at ../Python/generated_cases.c.h:3201  
        fromlist = 0x7fffec56c250  
        level = 0x7ffff04dfa90 <_PyRuntime+14000>  
        res = <optimized out>  
        name = 0x7fffec570eb0  
        opcode = <optimized out>  
        oparg = <optimized out>  
        entry_frame = {f_executable = 0x7ffff04a5340 <_Py_NoneStruct>, previous = 0x0, f_funcobj = 0xeb0f6040, f_globals = 0x7fffec9d47b0, f_builtins = 0x7ffff021a3e2,   
          f_locals = 0x7ffff78d6d53 <___interceptor___tls_get_addr(void*)+99>, frame_obj = 0x7fffffff4610, instr_ptr = 0x7ffff02818e2 <_Py_INTERPRETER_TRAMPOLINE_INSTRUCTIONS+2>,   
          stacktop = 0, return_offset = 0, owner = 3 '\003', localsplus = {0x7fffeb0f6040}}  
        next_instr = 0x7fffec9d4882  
        stack_pointer = <optimized out>  
        exception_unwind = <optimized out>  
        dying = <optimized out>  
        __func__ = "_PyEval_EvalFrameDefault"  
        opcode_targets = {0x7ffff004ccd3 <_PyEval_EvalFrameDefault+51827>, 0x7ffff0047e94 <_PyEval_EvalFrameDefault+31796>, 0x7ffff00474d5 <_PyEval_EvalFrameDefault+29301>,   
          0x7ffff0049208 <_PyEval_EvalFrameDefault+36776>, 0x7ffff0044d27 <_PyEval_EvalFrameDefault+19143>, 0x7ffff0044dd6 <_PyEval_EvalFrameDefault+19318>,   
          0x7ffff004278b <_PyEval_EvalFrameDefault+9515>, 0x7ffff0042884 <_PyEval_EvalFrameDefault+9764>, 0x7ffff00432b6 <_PyEval_EvalFrameDefault+12374>,   
          0x7ffff0041286 <_PyEval_EvalFrameDefault+4134>, 0x7ffff00410c4 <_PyEval_EvalFrameDefault+3684>, 0x7ffff0041154 <_PyEval_EvalFrameDefault+3828>,   
          0x7ffff0041333 <_PyEval_EvalFrameDefault+4307>, 0x7ffff004138d <_PyEval_EvalFrameDefault+4397>, 0x7ffff0041460 <_PyEval_EvalFrameDefault+4608>,   
          0x7ffff00413cd <_PyEval_EvalFrameDefault+4461>, 0x7ffff0043039 <_PyEval_EvalFrameDefault+11737>, 0x7ffff004ccb8 <_PyEval_EvalFrameDefault+51800>,   
          0x7ffff00445fd <_PyEval_EvalFrameDefault+17309>, 0x7ffff00444a8 <_PyEval_EvalFrameDefault+16968>, 0x7ffff004290c <_PyEval_EvalFrameDefault+9900>,   
          0x7ffff00443a1 <_PyEval_EvalFrameDefault+16705>, 0x7ffff0044068 <_PyEval_EvalFrameDefault+15880>, 0x7ffff00430d7 <_PyEval_EvalFrameDefault+11895>,   
          0x7ffff0045ee9 <_PyEval_EvalFrameDefault+23689>, 0x7ffff00464a7 <_PyEval_EvalFrameDefault+25159>, 0x7ffff0046e8d <_PyEval_EvalFrameDefault+27693>,   
          0x7ffff0046d91 <_PyEval_EvalFrameDefault+27441>, 0x7ffff0046de1 <_PyEval_EvalFrameDefault+27521>, 0x7ffff0046e37 <_PyEval_EvalFrameDefault+27607>,   
          0x7ffff00469ac <_PyEval_EvalFrameDefault+26444>, 0x7ffff00469d1 <_PyEval_EvalFrameDefault+26481>, 0x7ffff0046c1c <_PyEval_EvalFrameDefault+27068>,   
          0x7ffff0046c66 <_PyEval_EvalFrameDefault+27142>, 0x7ffff0045eb0 <_PyEval_EvalFrameDefault+23632>, 0x7ffff00488d5 <_PyEval_EvalFrameDefault+34421>,   
          0x7ffff0049502 <_PyEval_EvalFrameDefault+37538>, 0x7ffff004714a <_PyEval_EvalFrameDefault+28394>, 0x7ffff00456a8 <_PyEval_EvalFrameDefault+21576>,   
          0x7ffff004576a <_PyEval_EvalFrameDefault+21770>, 0x7ffff00458a0 <_PyEval_EvalFrameDefault+22080>, 0x7ffff0047618 <_PyEval_EvalFrameDefault+29624>,   
          0x7ffff004767e <_PyEval_EvalFrameDefault+29726>, 0x7ffff00476e4 <_PyEval_EvalFrameDefault+29828>, 0x7ffff0046855 <_PyEval_EvalFrameDefault+26101>,   
          0x7ffff0047f61 <_PyEval_EvalFrameDefault+32001>, 0x7ffff0045d33 <_PyEval_EvalFrameDefault+23251>, 0x7ffff0045e47 <_PyEval_EvalFrameDefault+23527>,   
          0x7ffff004923b <_PyEval_EvalFrameDefault+36827>, 0x7ffff004958a <_PyEval_EvalFrameDefault+37674>, 0x7ffff004872a <_PyEval_EvalFrameDefault+33994>,   
          0x7ffff0048801 <_PyEval_EvalFrameDefault+34209>, 0x7ffff004941e <_PyEval_EvalFrameDefault+37310>, 0x7ffff0049487 <_PyEval_EvalFrameDefault+37415>,   
          0x7ffff00409f5 <_PyEval_EvalFrameDefault+1941>, 0x7ffff00475a2 <_PyEval_EvalFrameDefault+29506>, 0x7ffff0047950 <_PyEval_EvalFrameDefault+30448>,   
          0x7ffff0043844 <_PyEval_EvalFrameDefault+13796>, 0x7ffff0043e56 <_PyEval_EvalFrameDefault+15350>, 0x7ffff0043a98 <_PyEval_EvalFrameDefault+14392>,   
          0x7ffff00437d4 <_PyEval_EvalFrameDefault+13684>, 0x7ffff0040e27 <_PyEval_EvalFrameDefault+3015>, 0x7ffff0040e75 <_PyEval_EvalFrameDefault+3093>,   
          0x7ffff0040ed3 <_PyEval_EvalFrameDefault+3187>, 0x7ffff0040f74 <_PyEval_EvalFrameDefault+3348>, 0x7ffff0040fcc <_PyEval_EvalFrameDefault+3436>,   
          0x7ffff0041025 <_PyEval_EvalFrameDefault+3525>, 0x7ffff0041074 <_PyEval_EvalFrameDefault+3604>, 0x7ffff004119e <_PyEval_EvalFrameDefault+3902>,   
          0x7ffff004121c <_PyEval_EvalFrameDefault+4028>, 0x7ffff004d14c <_PyEval_EvalFrameDefault+52972>, 0x7ffff004130b <_PyEval_EvalFrameDefault+4267>,   
          0x7ffff00414d9 <_PyEval_EvalFrameDefault+4729>, 0x7ffff0044426 <_PyEval_EvalFrameDefault+16838>, 0x7ffff0044207 <_PyEval_EvalFrameDefault+16295>,   
          0x7ffff0044262 <_PyEval_EvalFrameDefault+16386>, 0x7ffff0044095 <_PyEval_EvalFrameDefault+15925>, 0x7ffff0041721 <_PyEval_EvalFrameDefault+5313>,   
          0x7ffff0041761 <_PyEval_EvalFrameDefault+5377>, 0x7ffff004178d <_PyEval_EvalFrameDefault+5421>, 0x7ffff00417b5 <_PyEval_EvalFrameDefault+5461>,   
          0x7ffff0041811 <_PyEval_EvalFrameDefault+5553>, 0x7ffff0042dfb <_PyEval_EvalFrameDefault+11163>, 0x7ffff0045f56 <_PyEval_EvalFrameDefault+23798>,   
          0x7ffff0045fa3 <_PyEval_EvalFrameDefault+23875>, 0x7ffff0045ff6 <_PyEval_EvalFrameDefault+23958>, 0x7ffff004603c <_PyEval_EvalFrameDefault+24028>,   
          0x7ffff0046086 <_PyEval_EvalFrameDefault+24102>, 0x7ffff00460d5 <_PyEval_EvalFrameDefault+24181>, 0x7ffff004613e <_PyEval_EvalFrameDefault+24286>,   
          0x7ffff00461d8 <_PyEval_EvalFrameDefault+24440>, 0x7ffff004626f <_PyEval_EvalFrameDefault+24591>, 0x7ffff00464f2 <_PyEval_EvalFrameDefault+25234>,   
          0x7ffff0046575 <_PyEval_EvalFrameDefault+25365>, 0x7ffff0044cc6 <_PyEval_EvalFrameDefault+19046>, 0x7ffff0046f05 <_PyEval_EvalFrameDefault+27813>,   
          0x7ffff0046cd6 <_PyEval_EvalFrameDefault+27254>, 0x7ffff0046a3c <_PyEval_EvalFrameDefault+26588>, 0x7ffff0046a9c <_PyEval_EvalFrameDefault+26684>,   
          0x7ffff0046b2e <_PyEval_EvalFrameDefault+26830>, 0x7ffff0046bbc <_PyEval_EvalFrameDefault+26972>, 0x7ffff00494a1 <_PyEval_EvalFrameDefault+37441>,   
          0x7ffff0047a6e <_PyEval_EvalFrameDefault+30734>, 0x7ffff0047435 <_PyEval_EvalFrameDefault+29141>, 0x7ffff0046f5a <_PyEval_EvalFrameDefault+27898>,   
          0x7ffff00471c9 <_PyEval_EvalFrameDefault+28521>, 0x7ffff0047237 <_PyEval_EvalFrameDefault+28631>, 0x7ffff0047293 <_PyEval_EvalFrameDefault+28723>,   
          0x7ffff00450ee <_PyEval_EvalFrameDefault+20110>, 0x7ffff00453d3 <_PyEval_EvalFrameDefault+20851>, 0x7ffff004542f <_PyEval_EvalFrameDefault+20943>,   
          0x7ffff004548f <_PyEval_EvalFrameDefault+21039>, 0x7ffff0045509 <_PyEval_EvalFrameDefault+21161>, 0x7ffff00455a2 <_PyEval_EvalFrameDefault+21314>,   
          0x7ffff0045618 <_PyEval_EvalFrameDefault+21432>, 0x7ffff004585e <_PyEval_EvalFrameDefault+22014>, 0x7ffff0047727 <_PyEval_EvalFrameDefault+29895>,   
          0x7ffff0046645 <_PyEval_EvalFrameDefault+25573>, 0x7ffff0046914 <_PyEval_EvalFrameDefault+26292>, 0x7ffff0048d1c <_PyEval_EvalFrameDefault+35516> <repeats 30 times>,   
          0x7ffff00473f5 <_PyEval_EvalFrameDefault+29077>, 0x7ffff0047b0a <_PyEval_EvalFrameDefault+30890>, 0x7ffff0047cb0 <_PyEval_EvalFrameDefault+31312>,   
          0x7ffff0047da2 <_PyEval_EvalFrameDefault+31554>, 0x7ffff0048ebc <_PyEval_EvalFrameDefault+35932>, 0x7ffff0048f56 <_PyEval_EvalFrameDefault+36086>,   
          0x7ffff0049048 <_PyEval_EvalFrameDefault+36328>, 0x7ffff00490e2 <_PyEval_EvalFrameDefault+36482>, 0x7ffff0044f96 <_PyEval_EvalFrameDefault+19766>,   
          0x7ffff00489de <_PyEval_EvalFrameDefault+34686>, 0x7ffff0048a17 <_PyEval_EvalFrameDefault+34743>, 0x7ffff0048a9f <_PyEval_EvalFrameDefault+34879>,  
          0x7ffff0048bc8 <_PyEval_EvalFrameDefault+35176>, 0x7ffff00479f3 <_PyEval_EvalFrameDefault+30611>, 0x7ffff0047a3a <_PyEval_EvalFrameDefault+30682>,   
          0x7ffff00491d4 <_PyEval_EvalFrameDefault+36724>, 0x7ffff0046714 <_PyEval_EvalFrameDefault+25780>, 0x7ffff00484c8 <_PyEval_EvalFrameDefault+33384>,   
          0x7ffff00485f1 <_PyEval_EvalFrameDefault+33681>, 0x7ffff0047823 <_PyEval_EvalFrameDefault+30147>, 0x7ffff00492f6 <_PyEval_EvalFrameDefault+37014>,   
          0x7ffff0043855 <_PyEval_EvalFrameDefault+13813>, 0x7ffff004394e <_PyEval_EvalFrameDefault+14062>, 0x7ffff00446a4 <_PyEval_EvalFrameDefault+17476>,   
          0x7ffff00447e0 <_PyEval_EvalFrameDefault+17792>, 0x7ffff0044918 <_PyEval_EvalFrameDefault+18104>, 0x7ffff0044a48 <_PyEval_EvalFrameDefault+18408>,   
          0x7ffff004832a <_PyEval_EvalFrameDefault+32970>, 0x7ffff0042d1d <_PyEval_EvalFrameDefault+10941>, 0x7ffff0042530 <_PyEval_EvalFrameDefault+8912>,   
          0x7ffff0042d51 <_PyEval_EvalFrameDefault+10993>, 0x7ffff00420ec <_PyEval_EvalFrameDefault+7820>, 0x7ffff0042700 <_PyEval_EvalFrameDefault+9376>,   
          0x7ffff0043c2b <_PyEval_EvalFrameDefault+14795>, 0x7ffff0043e74 <_PyEval_EvalFrameDefault+15380>, 0x7ffff0042f22 <_PyEval_EvalFrameDefault+11458>,   
          0x7ffff0043992 <_PyEval_EvalFrameDefault+14130>, 0x7ffff0043b57 <_PyEval_EvalFrameDefault+14583>, 0x7ffff004162d <_PyEval_EvalFrameDefault+5069>,   
          0x7ffff004450e <_PyEval_EvalFrameDefault+17070>, 0x7ffff0043fe8 <_PyEval_EvalFrameDefault+15752>, 0x7ffff0044549 <_PyEval_EvalFrameDefault+17129>,   
          0x7ffff0043724 <_PyEval_EvalFrameDefault+13508>, 0x7ffff0042964 <_PyEval_EvalFrameDefault+9988>, 0x7ffff0042b14 <_PyEval_EvalFrameDefault+10420>,   
          0x7ffff0042bdb <_PyEval_EvalFrameDefault+10619>, 0x7ffff0042c1c <_PyEval_EvalFrameDefault+10684>, 0x7ffff0042c8b <_PyEval_EvalFrameDefault+10795>,   
          0x7ffff00481db <_PyEval_EvalFrameDefault+32635>, 0x7ffff00482b1 <_PyEval_EvalFrameDefault+32849>, 0x7ffff0047f7f <_PyEval_EvalFrameDefault+32031>,   
          0x7ffff0040597 <_PyEval_EvalFrameDefault+823>, 0x7ffff0048017 <_PyEval_EvalFrameDefault+32183>, 0x7ffff00480c8 <_PyEval_EvalFrameDefault+32360>,   
          0x7ffff004636b <_PyEval_EvalFrameDefault+24843>, 0x7ffff00463fc <_PyEval_EvalFrameDefault+24988>, 0x7ffff004658f <_PyEval_EvalFrameDefault+25391>,   
          0x7ffff0044ba0 <_PyEval_EvalFrameDefault+18752>, 0x7ffff00473bc <_PyEval_EvalFrameDefault+29020>, 0x7ffff0047086 <_PyEval_EvalFrameDefault+28198>,   
          0x7ffff0047301 <_PyEval_EvalFrameDefault+28833>, 0x7ffff0045051 <_PyEval_EvalFrameDefault+19953>, 0x7ffff0045294 <_PyEval_EvalFrameDefault+20532>,   
          0x7ffff0045a21 <_PyEval_EvalFrameDefault+22465>, 0x7ffff0045b51 <_PyEval_EvalFrameDefault+22769>, 0x7ffff0045aa3 <_PyEval_EvalFrameDefault+22595>,   
          0x7ffff0045b12 <_PyEval_EvalFrameDefault+22706>, 0x7ffff0045c15 <_PyEval_EvalFrameDefault+22965>, 0x7ffff00477b2 <_PyEval_EvalFrameDefault+30034>,   
          0x7ffff0045c8c <_PyEval_EvalFrameDefault+23084>, 0x7ffff0045cb7 <_PyEval_EvalFrameDefault+23127>...}  
        word = <optimized out>  
        word = <optimized out>  
#59 0x00007ffff00401a8 in PyEval_EvalCode (co=0x7fffec9d47b0, globals=<optimized out>, locals=0x7fffec570c40) at ../Python/ceval.c:602  
        tstate = 0x7fffeb0f6040  
        builtins = <optimized out>  
        desc = {fc_globals = 0x7fffec570c40, fc_builtins = 0x7fffecc392c0, fc_name = 0x7ffff04e4bb8 <_PyRuntime+34776>, fc_qualname = 0x7ffff04e4bb8 <_PyRuntime+34776>,   
          fc_code = 0x7fffec9d47b0, fc_defaults = 0x0, fc_kwdefaults = 0x0, fc_closure = 0x0}  
        func = 0x7fffec5682c0  
        res = <optimized out>  
#60 0x00007ffff00b677e in run_eval_code_obj (tstate=0x7fffeb0f6040, co=0x7fffec9d47b0, globals=0x7fffec570c40, locals=0x7fffec570c40) at ../Python/pythonrun.c:1337  
        v = <optimized out>  
        has_builtins = <optimized out>  
        v = <optimized out>  
        has_builtins = <optimized out>  
#61 run_eval_code_obj (tstate=tstate@entry=0x7fffeb0f6040, co=co@entry=0x7fffec9d47b0, globals=globals@entry=0x7fffec570c40, locals=locals@entry=0x7fffec570c40)  
    at ../Python/pythonrun.c:1304  
        v = <optimized out>  
        has_builtins = <optimized out>  
#62 0x00007ffff00b6977 in run_mod (mod=mod@entry=0x5250000d5ac0, filename=filename@entry=0x7ffff04e4c58 <_PyRuntime+34936>, globals=globals@entry=0x7fffec570c40,   
    locals=locals@entry=0x7fffec570c40, flags=flags@entry=0x0, arena=arena@entry=0x7fffec92ce10, interactive_src=0x0, generate_new_source=0) at ../Python/pythonrun.c:1422  
        tstate = 0x7fffeb0f6040  
        interactive_filename = <optimized out>  
        co = 0x7fffec9d47b0  
        v = <optimized out>  
#63 0x00007ffff00b7233 in _PyRun_StringFlagsWithName (generate_new_source=0, str=<optimized out>, name=<optimized out>, start=<optimized out>, globals=0x7fffec570c40,   
    locals=0x7fffec570c40, flags=0x0) at ../Python/pythonrun.c:1221  
        ret = 0x0  
        mod = 0x5250000d5ac0  
        arena = 0x7fffec92ce10  
        source = 0x0  
        ret = <optimized out>  
        mod = <optimized out>  
        arena = <optimized out>  
        source = <optimized out>  
#64 PyRun_StringFlags (str=<optimized out>, start=<optimized out>, globals=0x7fffec570c40, locals=0x7fffec570c40, flags=0x0) at ../Python/pythonrun.c:1233  
No locals.  
#65 0x00007ffff06d0658 in boost::python::exec (  
    string=0x5190001e2d80 "try:\n    from focs._tech import *\nexcept ModuleNotFoundError:\n    pass\n\nCategory(\n    name=\"LEARNING_CATEGORY\",\n    graphic=\"learning.png\",\n    colour=(54, 202, 229),\n)\n\nCategory(\n    name=\"GROWTH_CAT"..., global=..., local=...) at ./boost/python/object_core.hpp:440  
        s = 0x5190001e2d80 "try:\n    from focs._tech import *\nexcept ModuleNotFoundError:\n    pass\n\nCategory(\n    name=\"LEARNING_CATEGORY\",\n    graphic=\"learning.png\",\n    colour=(54, 202, 229),\n)\n\nCategory(\n    name=\"GROWTH_CAT"...  
        result = <optimized out>  
#66 0x00005555576694cb in PythonParser::ParseFileCommon (this=0x7fffeba04a70, path=..., globals=..., filename="/mnt/another/builds/freeorion-dbg/test-scripting/techs/Categories.inf.py",   
    file_contents="try:\n    from focs._tech import *\nexcept ModuleNotFoundError:\n    pass\n\nCategory(\n    name=\"LEARNING_CATEGORY\",\n    graphic=\"learning.png\",\n    colour=(54, 202, 229),\n)\n\nCategory(\n    name=\"GROWTH_CAT"...) at /home/oleg/devel/freeorion/parse/PythonParser.cpp:370  
        read_success = true  
(gdb) f 10  
#10 0x0000555557676224 in boost::python::to_python_value<bool const&>::operator() (this=0x7fffeb29e0b0, x=@0x7fffeb836080: false)  
    at /usr/include/boost/python/converter/builtin_converters.hpp:113  
113	BOOST_PYTHON_TO_PYTHON_BY_VALUE(bool, ::PyBool_FromLong(x), &PyBool_Type)  
(gdb) l  
108	        : ::PyInt_FromLong(x), &PyInt_Type)  
109	#endif  
110	  
111	// Bool is not signed.  
112	#if PY_VERSION_HEX >= 0x02030000  
113	BOOST_PYTHON_TO_PYTHON_BY_VALUE(bool, ::PyBool_FromLong(x), &PyBool_Type)  
114	#else  
115	BOOST_PYTHON_TO_PYTHON_BY_VALUE(bool, ::PyInt_FromLong(x), &PyInt_Type)  
116	#endif  
117	    
(gdb) macro expand BOOST_PYTHON_TO_PYTHON_BY_VALUE(bool, ::PyBool_FromLong(x), &PyBool_Type)  
expands to: template <> struct to_python_value<bool&> : detail::builtin_to_python { inline PyObject* operator()(bool const& x) const { return (::PyBool_FromLong(x)); } inline PyTypeObject const* get_pytype() const { return (&PyBool_Type); } }; template <> struct to_python_value<bool const&> : detail::builtin_to_python { inline PyObject* operator()(bool const& x) const { return (::PyBool_FromLong(x)); } inline PyTypeObject const* get_pytype() const { return (&PyBool_Type); } }; namespace converter { template <> struct arg_to_python< bool > : handle<> { arg_to_python(bool const& x) : python::handle<>(::PyBool_FromLong(x)) {} }; }  
(gdb) f 11  
#11 0x00005555576dfc4c in boost::python::detail::invoke<boost::python::to_python_value<bool const&>, boost::python::detail::datum<bool const> > (rc=..., f=...)  
    at /usr/include/boost/python/detail/invoke.hpp:73  
73	    return rc(f( BOOST_PP_ENUM_BINARY_PARAMS_Z(1, N, ac, () BOOST_PP_INTERCEPT) ));  
(gdb) l  
68	# define N BOOST_PP_ITERATION()  
69	  
70	template <class RC, class F BOOST_PP_ENUM_TRAILING_PARAMS_Z(1, N, class AC)>  
71	inline PyObject* invoke(invoke_tag_<false,false>, RC const& rc, F& f BOOST_PP_ENUM_TRAILING_BINARY_PARAMS_Z(1, N, AC, & ac) )  
72	{  
73	    return rc(f( BOOST_PP_ENUM_BINARY_PARAMS_Z(1, N, ac, () BOOST_PP_INTERCEPT) ));  
74	}  
75	                   
76	template <class RC, class F BOOST_PP_ENUM_TRAILING_PARAMS_Z(1, N, class AC)>  
77	inline PyObject* invoke(invoke_tag_<true,false>, RC const&, F& f BOOST_PP_ENUM_TRAILING_BINARY_PARAMS_Z(1, N, AC, & ac) )  
(gdb) macro expand return rc(f( BOOST_PP_ENUM_BINARY_PARAMS_Z(1, N, ac, () BOOST_PP_INTERCEPT) ));  
expands to: return rc(f( BOOST_PP_REPEAT_1_N(BOOST_PP_ENUM_BINARY_PARAMS_M, (ac, () BOOST_PP_INTERCEPT_)) ));  
(gdb) p rc  
$14 = (const boost::python::to_python_value<bool const&> &) @0x7fffeb29e0b0: {<boost::python::detail::builtin_to_python> = {static uses_registry = false}, <No data fields>}  
(gdb) p f  
$15 = (boost::python::detail::datum<bool const> &) @0x50200001acb8: {m_which = 0x7fffeb836080}  
(gdb) p f->m_which   
$16 = (const bool *) 0x7fffeb836080  
(gdb) p *(f->m_which )  
$17 = false  
(gdb) f 12  
#12 0x00005555576d95cc in boost::python::detail::caller_arity<0u>::impl<boost::python::detail::datum<bool const>, boost::python::return_value_policy<boost::python::return_by_value, boost::python::default_call_policies>, boost::mpl::vector1<bool const&> >::operator() (this=0x50200001acb8, args_=0x7ffff04f1cc8 <_PyRuntime+88296>)  
    at /usr/include/boost/python/detail/caller.hpp:233  
233	            PyObject* result = detail::invoke(  
(gdb) l  
228	            // all converters have been checked. Now we can do the  
229	            // precall part of the policy  
230	            if (!m_data.second().precall(inner_args))  
231	                return 0;  
232	  
233	            PyObject* result = detail::invoke(  
234	                detail::invoke_tag<result_t,F>()  
235	              , create_result_converter(args_, (result_converter*)0, (result_converter*)0)  
236	              , m_data.first()  
237	                BOOST_PP_ENUM_TRAILING_PARAMS(N, c)  
238	            );  
239	              
240	            return m_data.second().postcall(inner_args, result);  
241	        }  
242	  
243	        static unsigned min_arity() { return N; }  
244	          
245	        static py_func_sig_info  signature()  
246	        {  
247	            const signature_element * sig = detail::signature<Sig>::elements();  
(gdb) p m_data  
$19 = {<boost::details::compressed_pair_imp<boost::python::detail::datum<bool const>, boost::python::return_value_policy<boost::python::return_by_value, boost::python::default_call_policies>, 2>> = {<boost::python::return_value_policy<boost::python::return_by_value, boost::python::default_call_policies>> = {<boost::python::default_call_policies> = {<No data fields>}, <No data fields>}, first_ = {m_which = 0x7fffeb836080}}, <No data fields>}  
(gdb) p m_data.first_  
$21 = {m_which = 0x7fffeb836080}  
```

---

## Comment 3

> Username: o01eg  
> Created at: 2025-03-19 19:03:24 UTC  
> Url: https://github.com/boostorg/python/issues/413#issuecomment-2737729637  

Ok, it seemed to be an issues with passing literal to `def_readonly`.
