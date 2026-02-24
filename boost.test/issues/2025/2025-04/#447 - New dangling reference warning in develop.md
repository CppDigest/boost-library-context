# #447 - New dangling reference warning in develop [Open]

> Username: jzmaddock  
> Created at: 2025-04-21 14:53:51 UTC  
> Updated at: 2025-08-21 07:59:40 UTC  
> Url: https://github.com/boostorg/test/issues/447  

After updating to develop I see:  
  
```  
gcc.compile.c++ ..\..\..\bin.v2\libs\test\build\gcc-13\debug\x86_64\cxxstd-17-gnu\link-static\pch-off\threadapi-win32\threading-multi\visibility-hidden\framework.o  
In file included from D:/data/boost/boost/libs/test/src/framework.cpp:16:  
..\..\../boost/test/impl/framework.ipp: In function 'bool boost::unit_test::framework::impl::parse_filters(boost::unit_test::test_unit_id, boost::unit_test::test_unit_id_list&, boost::unit_test::test_unit_id_list&)':  
..\..\../boost/test/impl/framework.ipp:403:37: warning: possibly dangling reference to a temporary [-Wdangling-reference]  
403 |     std::vector<std::string> const& filters = runtime_config::get<std::vector<std::string> >( runtime_config::btrt_run_filters );  
    |                                     ^~~~~~~                                                                     ..\..\../boost/test/impl/framework.ipp:403:93: note: the temporary was destroyed at the end of the full expression 'boost::unit_test::runtime_config::get<std::vector<std::__cxx11::basic_string<char> > >(boost::unit_test::basic_cstring<const char>(boost::unit_test::runtime_config::btrt_run_filters))'  
403 |     std::vector<std::string> const& filters = runtime_config::get<std::vector<std::string> >( runtime_config::btrt_run_filters );  
    |                                               ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
..\..\../boost/test/impl/framework.ipp: In function 'void boost::unit_test::framework::impl::setup_loggers()':  
..\..\../boost/test/impl/framework.ipp:1000:45: warning: possibly dangling reference to a temporary [-Wdangling-reference]  
1000 |             const std::vector<std::string>& v_output_format = runtime_config::get< std::vector<std::string> >( runtime_config::btrt_combined_logger ) ;  
     |                                             ^~~~~~~~~~~~~~~  
..\..\../boost/test/impl/framework.ipp:1000:110: note: the temporary was destroyed at the end of the full expression 'boost::unit_test::runtime_config::get<std::vector<std::__cxx11::basic_string<char> > >(boost::unit_test::basic_cstring<const char>(boost::unit_test::runtime_config::btrt_combined_logger))'  
1000 |             const std::vector<std::string>& v_output_format = runtime_config::get< std::vector<std::string> >( runtime_config::btrt_combined_logger ) ;  
     |                                                               ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~^~~  
```  
  
With gcc-13 Mingw.
