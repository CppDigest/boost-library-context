# #430 - dynamic link with clang causes boost::runtime::arg_type_mismatch [Closed]

> Username: kantan2015  
> Created at: 2024-09-24 08:24:36 UTC  
> Updated at: 2024-10-04 17:50:40 UTC  
> Closed at: 2024-10-04 17:50:40 UTC  
> Url: https://github.com/boostorg/test/issues/430  

When the library(v1.85.0) dynamically linked with clang, the boost::runtime::arg_type_mismatch exception can be thrown on getting the configuration parameters.  
  
```  
#define BOOST_TEST_DYN_LINK  
#include <boost/test/unit_test.hpp>  
#include <boost/test/unit_test_parameters.hpp>  
  
bool init_unit_test() {  
  using namespace boost::unit_test;  
  
  runtime_config::get<log_level>(runtime_config::btrt_log_level);        // OK  
  runtime_config::get<output_format>(runtime_config::btrt_log_format);   // NG  
  runtime_config::get<report_level>(runtime_config::btrt_report_level);  // NG  
  return true;  
}  
  
int main(int argc, char* argv[], char*[]) {  
  return boost::unit_test::unit_test_main(&init_unit_test, argc, argv);  
}  
```  
The program was compiled with -std=c++20  and showed the error on run time.  
  
`Test setup error: boost::runtime::arg_type_mismatch: Access with invalid type for argument corresponding to parameter log_format`  
  
With gcc(13.2), there is no problem.  
```  
clang++ --version  
clang version 17.0.6 (Red Hat 17.0.6-1.module_el8.10.0+3757+fc27b834)  
Target: x86_64-redhat-linux-gnu  
Thread model: posix  
InstalledDir: /usr/bin  
```  
This seems similar to the issue https://github.com/boostorg/test/issues/199 and confirmed fine with the library built with visibility=global.
