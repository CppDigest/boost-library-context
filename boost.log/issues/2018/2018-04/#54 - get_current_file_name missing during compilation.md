# #54 - get_current_file_name missing during compilation [Closed]

> Username: aaronovz1  
> Created at: 2018-04-23 22:03:26 UTC  
> Updated at: 2018-04-23 23:55:12 UTC  
> Closed at: 2018-04-23 23:55:12 UTC  
> Url: https://github.com/boostorg/log/issues/54  

I am testing upgrading from boost 1.64 to 1.67 and I am getting a compilation error under GCC 5.4:  
  
`error: 'boost::log::v2s_mt_posix::aux::locking_ptr<boost::log::v2s_mt_posix::sinks::text_file_backend, boost::recursive_mutex>::element_type {aka class boost::log::v2s_mt_posix::sinks::text_file_backend}' has no member named 'get_current_file_name'`  
  
It doesn't really make sense to me because using the rotate_file() function and some others works fine, and they are defined in the same class. I thought it was a precompile/obj issue so I cleaned out the entire project and recompiled but that did not fix it.
