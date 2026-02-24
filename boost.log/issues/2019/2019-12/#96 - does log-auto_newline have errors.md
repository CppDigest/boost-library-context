# #96 - does log-auto_newline have errors? [Closed]

> Username: jingcb  
> Created at: 2019-12-29 14:41:13 UTC  
> Updated at: 2019-12-29 20:04:55 UTC  
> Closed at: 2019-12-29 20:04:54 UTC  
> Url: https://github.com/boostorg/log/issues/96  

### I build boost::log  with the follow cmd:  
    ./bootstrap.sh --prefix= install path  
    ./b2 --layout=tagged variant=release link=static threading=multi runtime-link=shared  --with-regex --with-chrono --with-system --with-thread --with-date_time --with-timer --with-filesystem --with-log install  
  
### when I build code with boost log lib, the version 1.72.0 has this error:  
    Undefined symbols for architecture x86_64:  
    "boost::log::v2s_mt_posix::sinks::text_file_backend::construct(boost::filesystem::path const&, boost::filesystem::path const&, unsigned int, unsigned long, boost::log::v2s_mt_posix::aux::light_function<bool ()> const&, bool, bool)", referenced from:  
      void boost::log::v2s_mt_posix::sinks::text_file_backend::construct<boost::parameter::aux::arg_list<boost::parameter::aux::tagged_argument<boost::log::v2s_mt_posix::keywords::tag::file_name, std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> > const>, boost::parameter::aux::arg_list<boost::parameter::aux::tagged_argument<boost::log::v2s_mt_posix::keywords::tag::format, char const [25]>, boost::parameter::aux::arg_list<boost::parameter::aux::tagged_argument<boost::log::v2s_mt_posix::keywords::tag::rotation_size, int const>, boost::parameter::aux::arg_list<boost::parameter::aux::tagged_argument<boost::log::v2s_mt_posix::keywords::tag::time_based_rotation, boost::log::v2s_mt_posix::sinks::file::rotation_at_time_interval const>, boost::parameter::aux::empty_arg_list> > > > >(boost::parameter::aux::arg_list<boost::parameter::aux::tagged_argument<boost::log::v2s_mt_posix::keywords::tag::file_name, std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> > const>, boost::parameter::aux::arg_list<boost::parameter::aux::tagged_argument<boost::log::v2s_mt_posix::keywords::tag::format, char const [25]>, boost::parameter::aux::arg_list<boost::parameter::aux::tagged_argument<boost::log::v2s_mt_posix::keywords::tag::rotation_size, int const>, boost::parameter::aux::arg_list<boost::parameter::aux::tagged_argument<boost::log::v2s_mt_posix::keywords::tag::time_based_rotation, boost::log::v2s_mt_posix::sinks::file::rotation_at_time_interval const>, boost::parameter::aux::empty_arg_list> > > > const&) in test_tile.test_tile.o  
    ld: symbol(s) not found for architecture x86_64  
  
### but the version 1.70.0 is ok   
### and i compare the Undefined symbols,1.70.0 is   
    __ZN5boost3log12v2s_mt_posix5sinks17text_file_backend9constructERKNS_10filesystem4pathES7_jmRKNS1_3aux14light_functionIFbvEEEbb  
### 1.72.0 is   
    __ZN5boost3log12v2s_mt_posix5sinks17text_file_backend9constructERKNS_10filesystem4pathES7_jmRKNS1_3aux14light_functionIFbvEEENS2_17auto_newline_modeEbb

---

## Comment 1

> Username: Lastique  
> Created at: 2019-12-29 20:04:54 UTC  
> Url: https://github.com/boostorg/log/issues/96#issuecomment-569537387  

Most likely, you have mismatched Boost headers and libraries. Probably, headers from Boost 1.70 and libraries from 1.72. Use matching headers and libraries.
