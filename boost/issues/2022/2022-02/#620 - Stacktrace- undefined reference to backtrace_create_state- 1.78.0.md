# #620 - Stacktrace: undefined reference to backtrace_create_state: 1.78.0 [Closed]

> Username: maxtju  
> Created at: 2022-02-07 10:28:35 UTC  
> Updated at: 2022-02-07 11:18:12 UTC  
> Closed at: 2022-02-07 11:18:12 UTC  
> Url: https://github.com/boostorg/boost/issues/620  

Version: 1.78.0  
System: Ubunutu 18.04  
g++: 7.5.0  
  
Compilation commands:  
sh ./bootstrap.sh --prefix=/usr/local && \  
    ./b2 install threading=multi link=shared -j"$(nproc)" \  
       --with-system --with-thread --with-date_time --with-chrono \  
       --with-filesystem --with-program_options --with-regex  --with-stacktrace  
  
Problem:  
In function `boost::stacktrace::detail::construct_state(boost::stacktrace::detail::program_location const&)':  
/usr/local/include/boost/stacktrace/detail/libbacktrace_impls.hpp:104: undefined reference to `backtrace_create_state'  
/opt/nvidia/deepstream/deepstream-5.0/build//opt/nvidia/deepstream/deepstream-5.0/sources/apps/apps-common/src/logger/logger.o: In function `boost::stacktrace::detail::to_string_using_backtrace::prepare_function_name(void const*)':  
/usr/local/include/boost/stacktrace/detail/libbacktrace_impls.hpp:123: undefined reference to `backtrace_pcinfo'  
/usr/local/include/boost/stacktrace/detail/libbacktrace_impls.hpp:131: undefined reference to `backtrace_syminfo'

---

## Comment 1

> Username: maxtju  
> Created at: 2022-02-07 11:18:12 UTC  
> Url: https://github.com/boostorg/boost/issues/620#issuecomment-1031353757  

sorry... missed add -DPLATFORM_TEGRA to the compilation flags
