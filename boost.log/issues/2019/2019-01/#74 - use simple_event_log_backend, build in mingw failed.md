# #74 - use simple_event_log_backend, build in mingw failed [Closed]

> Username: yuwenyong  
> Created at: 2019-01-28 02:32:20 UTC  
> Updated at: 2019-01-28 16:26:49 UTC  
> Closed at: 2019-01-28 16:19:42 UTC  
> Url: https://github.com/boostorg/log/issues/74  

failed in link phase. Do I need to link any specific dll?

---

## Comment 1

> Username: Lastique  
> Created at: 2019-01-28 10:18:39 UTC  
> Url: https://github.com/boostorg/log/issues/74#issuecomment-458072106  

You'll have to provide more details.

---

## Comment 2

> Username: yuwenyong  
> Created at: 2019-01-28 14:29:49 UTC  
> Url: https://github.com/boostorg/log/issues/74#issuecomment-458153016  

CMakeFiles\net4cxx.dir/objects.a(sinks.cpp.obj): In function `net4cxx::SimpleEventLogSinkBuilder::createBackend() const':  
C:/msys64/home/Administrator/net4cxx/src/net4cxx/common/logging/sinks.cpp:135: undefined reference to `__imp__ZN5boost3log9v2_mt_nt65sinks30basic_simple_event_log_backendIcE20get_default_log_nameB5cxx11Ev'  
C:/msys64/home/Administrator/net4cxx/src/net4cxx/common/logging/sinks.cpp:138: undefined reference to `__imp__ZN5boost3log9v2_mt_nt65sinks30basic_simple_event_log_backendIcE23get_default_source_nameB5cxx11Ev'  
C:/msys64/home/Administrator/net4cxx/src/net4cxx/common/logging/sinks.cpp:152: undefined reference to `__imp__ZN5boost3log9v2_mt_nt65sinks30basic_simple_event_log_backendIcE21set_event_type_mapperERKNS1_3aux14light_functionIFNS2_9event_log10event_typeERKNS1_11record_viewEEEE'  
CMakeFiles\net4cxx.dir/objects.a(sinks.cpp.obj): In function `void boost::log::v2_mt_nt6::sinks::basic_simple_event_log_backend<char>::construct<boost::parameter::aux::arg_list<boost::parameter::aux::tagged_argument<boost::log::v2_mt_nt6::keywords::tag::registration, boost::log::v2_mt_nt6::sinks::event_log::registration_mode const>, boost::parameter::aux::arg_list<boost::parameter::aux::tagged_argument<boost::log::v2_mt_nt6::keywords::tag::log_name, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > >, boost::parameter::aux::arg_list<boost::parameter::aux::tagged_argument<boost::log::v2_mt_nt6::keywords::tag::log_source, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > >, boost::parameter::aux::empty_arg_list> > > >(boost::parameter::aux::arg_list<boost::parameter::aux::tagged_argument<boost::log::v2_mt_nt6::keywords::tag::registration, boost::log::v2_mt_nt6::sinks::event_log::registration_mode const>, boost::parameter::aux::arg_list<boost::parameter::aux::tagged_argument<boost::log::v2_mt_nt6::keywords::tag::log_name, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > >, boost::parameter::aux::arg_list<boost::parameter::aux::tagged_argument<boost::log::v2_mt_nt6::keywords::tag::log_source, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > >, boost::parameter::aux::empty_arg_list> > > const&)':  
C:/msys64/mingw64/include/boost/log/sinks/event_log_backend.hpp:483: undefined reference to `__imp__ZN5boost3log9v2_mt_nt65sinks30basic_simple_event_log_backendIcE23get_default_source_nameB5cxx11Ev'  
C:/msys64/mingw64/include/boost/log/sinks/event_log_backend.hpp:482: undefined reference to `__imp__ZN5boost3log9v2_mt_nt65sinks30basic_simple_event_log_backendIcE20get_default_log_nameB5cxx11Ev'  
C:/msys64/mingw64/include/boost/log/sinks/event_log_backend.hpp:480: undefined reference to `__imp__ZN5boost3log9v2_mt_nt65sinks30basic_simple_event_log_backendIcE9constructERKNSt7__cxx1112basic_stringIcSt11char_traitsIcESaIcEEESC_SC_NS2_9event_log17registration_modeE'  
CMakeFiles\net4cxx.dir/objects.a(sinks.cpp.obj): In function `void boost::log::v2_mt_nt6::sinks::basic_formatting_sink_frontend<char>::feed_record<boost::recursive_mutex, boost::log::v2_mt_nt6::sinks::basic_simple_event_log_backend<char> >(boost::log::v2_mt_nt6::record_view const&, boost::recursive_mutex&, boost::log::v2_mt_nt6::sinks::basic_simple_event_log_backend<char>&)':  
C:/msys64/mingw64/include/boost/log/sinks/basic_sink_frontend.hpp:463: undefined reference to `__imp__ZN5boost3log9v2_mt_nt65sinks30basic_simple_event_log_backendIcE7consumeERKNS1_11record_viewERKNSt7__cxx1112basic_stringIcSt11char_traitsIcESaIcEEE'  
CMakeFiles\net4cxx.dir/objects.a(sinks.cpp.obj): In function `boost::detail::sp_ms_deleter<boost::log::v2_mt_nt6::sinks::basic_simple_event_log_backend<char> >::destroy()':  
C:/msys64/mingw64/include/boost/smart_ptr/make_shared_object.hpp:59: undefined reference to `__imp__ZN5boost3log9v2_mt_nt65sinks30basic_simple_event_log_backendIcED1Ev'  
CMakeFiles\net4cxx.dir/objects.a(sinks.cpp.obj): In function `void boost::log::v2_mt_nt6::sinks::basic_formatting_sink_frontend<char>::feed_record<boost::log::v2_mt_nt6::aux::fake_mutex, boost::log::v2_mt_nt6::sinks::basic_simple_event_log_backend<char> >(boost::log::v2_mt_nt6::record_view const&, boost::log::v2_mt_nt6::aux::fake_mutex&, boost::log::v2_mt_nt6::sinks::basic_simple_event_log_backend<char>&)':  
C:/msys64/mingw64/include/boost/log/sinks/basic_sink_frontend.hpp:463: undefined reference to `__imp__ZN5boost3log9v2_mt_nt65sinks30basic_simple_event_log_backendIcE7consumeERKNS1_11record_viewERKNSt7__cxx1112basic_stringIcSt11char_traitsIcESaIcEEE'  
collect2.exe: error: ld returned 1 exit status

---

## Comment 3

> Username: Lastique  
> Created at: 2019-01-28 14:46:28 UTC  
> Url: https://github.com/boostorg/log/issues/74#issuecomment-458159347  

Are the errors related to event log the only linking errors you get? If so, most likely Boost.Log was built without event log support. See if `b2` output contains a line `- message-compiler` in the beginning. If it says `no` then you don't have a message compiler tool in your `PATH` that is compatible with Boost.Build.

---

## Comment 4

> Username: yuwenyong  
> Created at: 2019-01-28 15:20:59 UTC  
> Url: https://github.com/boostorg/log/issues/74#issuecomment-458172825  

These are the only errors.  
I install boost by running command "pacman -S mingw-w64-x86_64-boost". BOOST_LOG_WITHOUT_EVENT_LOG is not defined.  
It seems I have to build compile boost manually?

---

## Comment 5

> Username: Lastique  
> Created at: 2019-01-28 15:27:06 UTC  
> Url: https://github.com/boostorg/log/issues/74#issuecomment-458175176  

I don't know what `pacman` is. If this is some kind of a package manager, find out how the package was built. Or build Boost yourself.  
  
Though I have to say I remember there were problems with using `windmc` tool from MinGW with Boost.Build. I remember a few years back Boost.Build had to be patched to work with that tool, but I can't find a reference to the patch right now. This might be the reason why the package was built without event log support.

---

## Comment 6

> Username: Lastique  
> Created at: 2019-01-28 15:32:15 UTC  
> Url: https://github.com/boostorg/log/issues/74#issuecomment-458177162  

See [here](https://www.boost.org/doc/libs/1_69_0/libs/log/doc/html/log/installation.html#log.installation.supported_compilers.notes_for_mingw__cygwin_and_visual_studio_express_edition_users) and [here](https://svn.boost.org/trac10/ticket/4111).

---

## Comment 7

> Username: yuwenyong  
> Created at: 2019-01-28 15:38:26 UTC  
> Url: https://github.com/boostorg/log/issues/74#issuecomment-458179518  

Okay. I'm just wondering why BOOST_LOG_WITHOUT_EVENT_LOG is still undefined when mc tool can't be detected.

---

## Comment 8

> Username: Lastique  
> Created at: 2019-01-28 16:19:42 UTC  
> Url: https://github.com/boostorg/log/issues/74#issuecomment-458197206  

It is defined when the library is built. It cannot be defined by the library when your code is built because mc tool detection is only done when Boost.Log is compiled and re result of that detection doesn't transfer to the library sources.

---

## Comment 9

> Username: Lastique  
> Created at: 2019-01-28 16:26:49 UTC  
> Url: https://github.com/boostorg/log/issues/74#issuecomment-458200297  

Also, I should note that `BOOST_LOG_WITHOUT_EVENT_LOG` is a config macro, meaning that users can define it to disable event log support. The library defines it as part of its build process if it detects that event log cannot be supported. This macro is not defined by the library as a means to let users know that event log is not supported.
