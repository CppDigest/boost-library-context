# #46 - undefined reference to boost::log::v2_mt_posix [Closed]

> Username: bkda  
> Created at: 2017-12-27 01:55:29 UTC  
> Updated at: 2025-11-04 12:21:35 UTC  
> Closed at: 2017-12-27 06:21:45 UTC  
> Url: https://github.com/boostorg/log/issues/46  

Like this issue [issues/18](https://github.com/boostorg/log/issues/18).  
  
I tried to solve this problem, but don't work.Also I found this from stackoverflow [boost-logger-linking-issue](https://stackoverflow.com/questions/18881602/boost-logger-linking-issue). And still dont't work.  
  
Platform: mac os 10.13.1  
compiler: gcc version 4.9.4 (Homebrew GCC 4.9.4)  
  
I installed boost 1.66.0 by this:  
  
    ./bootstrap.sh --with-toolset=gcc --with-libraries=program_options,system,thread,regex,serialization,log,date_time  
  
cmakelists:  
  
> set(REQUIRED_BOOST_LIBRARIES)  
> list(APPEND REQUIRED_BOOST_LIBRARIES "log_setup" "log" "serialization" "system" "filesystem" "date_time")  
> set(Boost_USE_MULTITHREADED ON)  
> find_package(Boost  
>         COMPONENTS "${REQUIRED_BOOST_LIBRARIES}"  
>         REQUIRED  
>         )  
  
  
Error  
  
> Undefined symbols for architecture x86_64:  
>   "boost::log::v2s_mt_posix::attribute_name::get_id_from_string(char const*)", referenced from:  
>       init_framework_logging(std::basic_string<char, std::char_traits<char>, std::allocator<char> > const&) in logger.cpp.o  
>   "void boost::log::v2s_mt_posix::aux::put_integer<char>(boost::log::v2s_mt_posix::aux::basic_ostringstreambuf<char, std::char_traits<char>, std::allocator<char> >&, unsigned int, unsigned int, char)", referenced from:  
>       boost::log::v2s_mt_posix::aux::date_time_formatter<boost::log::v2s_mt_posix::aux::decomposed_time_wrapper<boost::posix_time::ptime>, char>::format_fractional_seconds(boost::log::v2s_mt_posix::aux::date_time_formatter<boost::log::v2s_mt_posix::aux::decomposed_time_wrapper<boost::posix_time::ptime>, char>::context&) in logger.cpp.o  
>       boost::log::v2s_mt_posix::aux::date_time_formatter<boost::log::v2s_mt_posix::aux::decomposed_time_wrapper<boost::posix_time::ptime>, char>::format_seconds(boost::log::v2s_mt_posix::aux::date_time_formatter<boost::log::v2s_mt_posix::aux::decomposed_time_wrapper<boost::posix_time::ptime>, char>::context&) in logger.cpp.o  
>       boost::log::v2s_mt_posix::aux::date_time_formatter<boost::log::v2s_mt_posix::aux::decomposed_time_wrapper<boost::posix_time::ptime>, char>::format_minutes(boost::log::v2s_mt_posix::aux::date_time_formatter<boost::log::v2s_mt_posix::aux::decomposed_time_wrapper<boost::posix_time::ptime>, char>::context&) in logger.cpp.o  
>       void boost::log::v2s_mt_posix::aux::date_time_formatter<boost::log::v2s_mt_posix::aux::decomposed_time_wrapper<boost::posix_time::ptime>, char>::format_hours_12<(char)32>(boost::log::v2s_mt_posix::aux::date_time_formatter<boost::log::v2s_mt_posix::aux::decomposed_time_wrapper<boost::posix_time::ptime>, char>::context&) in logger.cpp.o  
>       void boost::log::v2s_mt_posix::aux::date_time_formatter<boost::log::v2s_mt_posix::aux::decomposed_time_wrapper<boost::posix_time::ptime>, char>::format_hours_12<(char)48>(boost::log::v2s_mt_posix::aux::date_time_formatter<boost::log::v2s_mt_posix::aux::decomposed_time_wrapper<boost::posix_time::ptime>, char>::context&) in logger.cpp.o  
>       void boost::log::v2s_mt_posix::aux::date_time_formatter<boost::log::v2s_mt_posix::aux::decomposed_time_wrapper<boost::posix_time::ptime>, char>::format_hours<(char)32>(boost::log::v2s_mt_posix::aux::date_time_formatter<boost::log::v2s_mt_posix::aux::decomposed_time_wrapper<boost::posix_time::ptime>, char>::context&) in logger.cpp.o  
>       void boost::log::v2s_mt_posix::aux::date_time_formatter<boost::log::v2s_mt_posix::aux::decomposed_time_wrapper<boost::posix_time::ptime>, char>::format_hours<(char)48>(boost::log::v2s_mt_posix::aux::date_time_formatter<boost::log::v2s_mt_posix::aux::decomposed_time_wrapper<boost::posix_time::ptime>, char>::context&) in logger.cpp.o  
>       ...  
>   "boost::log::v2s_mt_posix::aux::this_thread::get_id()", referenced from:  
>       boost::log::v2s_mt_posix::attributes::current_thread_id::impl::dispatch(boost::log::v2s_mt_posix::type_dispatcher&) in logger.cpp.o  
>       boost::log::v2s_mt_posix::attributes::current_thread_id::impl::detach_from_thread()    in logger.cpp.o  
>   "boost::log::v2s_mt_posix::aux::this_process::get_id()", referenced from:  
>       boost::log::v2s_mt_posix::add_common_attributes()     in logger.cpp.o  
>   "boost::log::v2s_mt_posix::aux::code_convert_impl(wchar_t const*, unsigned long, std::basic_string<char, std::char_traits<char>, std::allocator<char> >&, unsigned long, std::locale const&)", referenced from:  
>       void boost::log::v2s_mt_posix::basic_formatting_ostream<char, std::char_traits<char>, std::allocator<char> >::aligned_write<wchar_t>(wchar_t const*, long) in logger.cpp.o  
>       void boost::log::v2s_mt_posix::type_dispatcher::callback_base::trampoline<boost::log::v2s_mt_posix::binder1st<boost::log::v2s_mt_posix::output_fun, boost::log::v2s_mt_posix::expressions::aux::stream_ref<boost::log::v2s_mt_posix::basic_formatting_ostream<char, std::char_traits<char>, std::allocator<char> > >&>, std::basic_string<wchar_t, std::char_traits<wchar_t>, std::allocator<wchar_t> > >(void*, std::basic_string<wchar_t, std::char_traits<wchar_t>, std::allocator<wchar_t> > const&) in logger.cpp.o  
>   "boost::log::v2s_mt_posix::aux::once_block_sentry::commit()", referenced from:  
>       boost::array<std::pair<boost::typeindex::stl_type_index, void*>, 2ul> const& boost::log::v2s_mt_posix::aux::type_sequence_dispatcher<boost::mpl::vector2<std::basic_string<char, std::char_traits<char>, std::allocator<char> >, std::basic_string<wchar_t, std::char_traits<wchar_t>, std::allocator<wchar_t> > > >::get_dispatching_map<boost::log::v2s_mt_posix::binder1st<boost::log::v2s_mt_posix::output_fun, boost::log::v2s_mt_posix::expressions::aux::stream_ref<boost::log::v2s_mt_posix::basic_formatting_ostream<char, std::char_traits<char>, std::allocator<char> > >&> >() in logger.cpp.o  
>   "boost::log::v2s_mt_posix::aux::once_block_sentry::rollback()", referenced from:  
>       boost::array<std::pair<boost::typeindex::stl_type_index, void*>, 2ul> const& boost::log::v2s_mt_posix::aux::type_sequence_dispatcher<boost::mpl::vector2<std::basic_string<char, std::char_traits<char>, std::allocator<char> >, std::basic_string<wchar_t, std::char_traits<wchar_t>, std::allocator<wchar_t> > > >::get_dispatching_map<boost::log::v2s_mt_posix::binder1st<boost::log::v2s_mt_posix::output_fun, boost::log::v2s_mt_posix::expressions::aux::stream_ref<boost::log::v2s_mt_posix::basic_formatting_ostream<char, std::char_traits<char>, std::allocator<char> > >&> >() in logger.cpp.o  
>   "void boost::log::v2s_mt_posix::aux::parse_date_time_format<char>(char const*, char const*, boost::log::v2s_mt_posix::aux::date_time_format_parser_callback<char>&)", referenced from:  
>       init_framework_logging(std::basic_string<char, std::char_traits<char>, std::allocator<char> > const&) in logger.cpp.o  
>   "boost::log::v2s_mt_posix::aux::default_attribute_names::process_id()", referenced from:  
>       boost::log::v2s_mt_posix::add_common_attributes()     in logger.cpp.o  
>   "boost::log::v2s_mt_posix::aux::default_attribute_names::line_id()", referenced from:  
>       boost::log::v2s_mt_posix::add_common_attributes()     in logger.cpp.o  
>   "boost::log::v2s_mt_posix::aux::default_attribute_names::message()", referenced from:  
>       init_framework_logging(std::basic_string<char, std::char_traits<char>, std::allocator<char> > const&) in logger.cpp.o  
>       boost::log::v2s_mt_posix::sinks::basic_formatting_sink_frontend<char>::basic_formatting_sink_frontend(bool) in logger.cpp.o  
>   "boost::log::v2s_mt_posix::aux::default_attribute_names::thread_id()", referenced from:  
>       boost::log::v2s_mt_posix::add_common_attributes()     in logger.cpp.o  
>   "boost::log::v2s_mt_posix::aux::default_attribute_names::timestamp()", referenced from:  
>       boost::log::v2s_mt_posix::add_common_attributes()     in logger.cpp.o  
>   "boost::log::v2s_mt_posix::aux::attach_attribute_name_info(boost::exception&, boost::log::v2s_mt_posix::attribute_name const&)", referenced from:  
>       boost::log::v2s_mt_posix::aux::light_function<bool (boost::log::v2s_mt_posix::attribute_value_set const&)>::impl<boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::greater_equal, boost::proto::argsns_::list2<boost::log::v2s_mt_posix::expressions::attribute_actor<boost::log::v2s_mt_posix::trivial::severity_level, boost::log::v2s_mt_posix::fallback_to_none, boost::log::v2s_mt_posix::trivial::tag::severity, boost::phoenix::actor>, boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<boost::log::v2s_mt_posix::trivial::severity_level>, 0l> > >, 2l> > >::invoke_impl(void*, boost::log::v2s_mt_posix::attribute_value_set const&) in logger.cpp.o  
>       boost::log::v2s_mt_posix::value_extractor<boost::log::v2s_mt_posix::trivial::severity_level, boost::log::v2s_mt_posix::fallback_to_none, boost::log::v2s_mt_posix::trivial::tag::severity>::operator()(boost::log::v2s_mt_posix::attribute_name const&, boost::log::v2s_mt_posix::attribute_value_set const&) const in logger.cpp.o  
>       boost::log::v2s_mt_posix::aux::light_function<void (boost::log::v2s_mt_posix::record_view const&, boost::log::v2s_mt_posix::expressions::aux::stream_ref<boost::log::v2s_mt_posix::basic_formatting_ostream<char, std::char_traits<char>, std::allocator<char> > >)>::impl<boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::shift_left, boost::proto::argsns_::list2<boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::shift_left, boost::proto::argsns_::list2<boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::shift_left, boost::proto::argsns_::list2<boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::shift_left, boost::proto::argsns_::list2<boost::phoenix::actor<boost::log::v2s_mt_posix::expressions::aux::attribute_output_terminal<boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<boost::phoenix::argument<2> >, 0l> >, boost::posix_time::ptime, boost::log::v2s_mt_posix::fallback_to_none, boost::log::v2s_mt_posix::aux::light_function<void (boost::log::v2s_mt_posix::basic_formatting_ostream<char, std::char_traits<char>, std::allocator<char> >&, boost::posix_time::ptime const&)> > >, boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<char [4]>, 0l> > >, 2l> >, boost::log::v2s_mt_posix::expressions::attribute_actor<boost::log::v2s_mt_posix::trivial::severity_level, boost::log::v2s_mt_posix::fallback_to_none, boost::log::v2s_mt_posix::trivial::tag::severity, boost::phoenix::actor> >, 2l> >, boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<char [3]>, 0l> > >, 2l> >, boost::log::v2s_mt_posix::expressions::attribute_actor<std::basic_string<char, std::char_traits<char>, std::allocator<char> >, boost::log::v2s_mt_posix::fallback_to_none, boost::log::v2s_mt_posix::expressions::tag::smessage, boost::phoenix::actor> >, 2l> > >::invoke_impl(void*, boost::log::v2s_mt_posix::record_view const&, boost::log::v2s_mt_posix::expressions::aux::stream_ref<boost::log::v2s_mt_posix::basic_formatting_ostream<char, std::char_traits<char>, std::allocator<char> > >) in logger.cpp.o  
>       boost::log::v2s_mt_posix::aux::light_function<void (boost::log::v2s_mt_posix::record_view const&, boost::log::v2s_mt_posix::expressions::aux::stream_ref<boost::log::v2s_mt_posix::basic_formatting_ostream<char, std::char_traits<char>, std::allocator<char> > >)>::impl<boost::log::v2s_mt_posix::expressions::aux::message_formatter>::invoke_impl(void*, boost::log::v2s_mt_posix::record_view const&, boost::log::v2s_mt_posix::expressions::aux::stream_ref<boost::log::v2s_mt_posix::basic_formatting_ostream<char, std::char_traits<char>, std::allocator<char> > >) in logger.cpp.o  
>   "boost::log::v2s_mt_posix::core::set_filter(boost::log::v2s_mt_posix::filter const&)", referenced from:  
>       init_framework_logging(std::basic_string<char, std::char_traits<char>, std::allocator<char> > const&) in logger.cpp.o  
>   "boost::log::v2s_mt_posix::core::add_global_attribute(boost::log::v2s_mt_posix::attribute_name const&, boost::log::v2s_mt_posix::attribute const&)", referenced from:  
>       boost::log::v2s_mt_posix::add_common_attributes()     in logger.cpp.o  
>   "boost::log::v2s_mt_posix::core::get()", referenced from:  
>       init_framework_logging(std::basic_string<char, std::char_traits<char>, std::allocator<char> > const&) in logger.cpp.o  
>       boost::log::v2s_mt_posix::add_common_attributes()     in logger.cpp.o  
>       boost::shared_ptr<boost::log::v2s_mt_posix::sinks::synchronous_sink<boost::log::v2s_mt_posix::sinks::text_file_backend> > boost::log::v2s_mt_posix::aux::add_file_log<boost::parameter::aux::arg_list<boost::parameter::aux::tagged_argument<boost::log::v2s_mt_posix::keywords::tag::format, boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::shift_left, boost::proto::argsns_::list2<boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::shift_left, boost::proto::argsns_::list2<boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::shift_left, boost::proto::argsns_::list2<boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::shift_left, boost::proto::argsns_::list2<boost::phoenix::actor<boost::log::v2s_mt_posix::expressions::aux::attribute_output_terminal<boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<boost::phoenix::argument<2> >, 0l> >, boost::posix_time::ptime, boost::log::v2s_mt_posix::fallback_to_none, boost::log::v2s_mt_posix::aux::light_function<void (boost::log::v2s_mt_posix::basic_formatting_ostream<char, std::char_traits<char>, std::allocator<char> >&, boost::posix_time::ptime const&)> > >, boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<char [4]>, 0l> > >, 2l> >, boost::log::v2s_mt_posix::expressions::attribute_actor<boost::log::v2s_mt_posix::trivial::severity_level, boost::log::v2s_mt_posix::fallback_to_none, boost::log::v2s_mt_posix::trivial::tag::severity, boost::phoenix::actor> >, 2l> >, boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<char [3]>, 0l> > >, 2l> >, boost::log::v2s_mt_posix::expressions::attribute_actor<std::basic_string<char, std::char_traits<char>, std::allocator<char> >, boost::log::v2s_mt_posix::fallback_to_none, boost::log::v2s_mt_posix::expressions::tag::smessage, boost::phoenix::actor> >, 2l> > const>, boost::parameter::aux::arg_list<boost::parameter::aux::tagged_argument<boost::log::v2s_mt_posix::keywords::tag::time_based_rotation, boost::log::v2s_mt_posix::sinks::file::rotation_at_time_point const>, boost::parameter::aux::arg_list<boost::parameter::aux::tagged_argument<boost::log::v2s_mt_posix::keywords::tag::rotation_size, int const>, boost::parameter::aux::arg_list<boost::parameter::aux::tagged_argument<boost::log::v2s_mt_posix::keywords::tag::auto_flush, bool const>, boost::parameter::aux::arg_list<boost::parameter::aux::tagged_argument<boost::log::v2s_mt_posix::keywords::tag::file_name, std::basic_string<char, std::char_traits<char>, std::allocator<char> > const>, boost::parameter::aux::arg_list<boost::parameter::aux::tagged_argument<boost::log::v2s_mt_posix::keywords::tag::open_mode, std::_Ios_Openmode const>, boost::parameter::aux::empty_arg_list> > > > > > >(boost::parameter::aux::arg_list<boost::parameter::aux::tagged_argument<boost::log::v2s_mt_posix::keywords::tag::format, boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::shift_left, boost::proto::argsns_::list2<boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::shift_left, boost::proto::argsns_::list2<boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::shift_left, boost::proto::argsns_::list2<boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::shift_left, boost::proto::argsns_::list2<boost::phoenix::actor<boost::log::v2s_mt_posix::expressions::aux::attribute_output_terminal<boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<boost::phoenix::argument<2> >, 0l> >, boost::posix_time::ptime, boost::log::v2s_mt_posix::fallback_to_none, boost::log::v2s_mt_posix::aux::light_function<void (boost::log::v2s_mt_posix::basic_formatting_ostream<char, std::char_traits<char>, std::allocator<char> >&, boost::posix_time::ptime const&)> > >, boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<char [4]>, 0l> > >, 2l> >, boost::log::v2s_mt_posix::expressions::attribute_actor<boost::log::v2s_mt_posix::trivial::severity_level, boost::log::v2s_mt_posix::fallback_to_none, boost::log::v2s_mt_posix::trivial::tag::severity, boost::phoenix::actor> >, 2l> >, boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<char [3]>, 0l> > >, 2l> >, boost::log::v2s_mt_posix::expressions::attribute_actor<std::basic_string<char, std::char_traits<char>, std::allocator<char> >, boost::log::v2s_mt_posix::fallback_to_none, boost::log::v2s_mt_posix::expressions::tag::smessage, boost::phoenix::actor> >, 2l> > const>, boost::parameter::aux::arg_list<boost::parameter::aux::tagged_argument<boost::log::v2s_mt_posix::keywords::tag::time_based_rotation, boost::log::v2s_mt_posix::sinks::file::rotation_at_time_point const>, boost::parameter::aux::arg_list<boost::parameter::aux::tagged_argument<boost::log::v2s_mt_posix::keywords::tag::rotation_size, int const>, boost::parameter::aux::arg_list<boost::parameter::aux::tagged_argument<boost::log::v2s_mt_posix::keywords::tag::auto_flush, bool const>, boost::parameter::aux::arg_list<boost::parameter::aux::tagged_argument<boost::log::v2s_mt_posix::keywords::tag::file_name, std::basic_string<char, std::char_traits<char>, std::allocator<char> > const>, boost::parameter::aux::arg_list<boost::parameter::aux::tagged_argument<boost::log::v2s_mt_posix::keywords::tag::open_mode, std::_Ios_Openmode const>, boost::parameter::aux::empty_arg_list> > > > > > const&) in logger.cpp.o  
>       boost::shared_ptr<boost::log::v2s_mt_posix::sinks::synchronous_sink<boost::log::v2s_mt_posix::sinks::basic_text_ostream_backend<char> > > boost::log::v2s_mt_posix::aux::add_console_log<char, boost::parameter::aux::tagged_argument<boost::log::v2s_mt_posix::keywords::tag::format, boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::shift_left, boost::proto::argsns_::list2<boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::shift_left, boost::proto::argsns_::list2<boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::shift_left, boost::proto::argsns_::list2<boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::shift_left, boost::proto::argsns_::list2<boost::phoenix::actor<boost::log::v2s_mt_posix::expressions::aux::attribute_output_terminal<boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<boost::phoenix::argument<2> >, 0l> >, boost::posix_time::ptime, boost::log::v2s_mt_posix::fallback_to_none, boost::log::v2s_mt_posix::aux::light_function<void (boost::log::v2s_mt_posix::basic_formatting_ostream<char, std::char_traits<char>, std::allocator<char> >&, boost::posix_time::ptime const&)> > >, boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<char [4]>, 0l> > >, 2l> >, boost::log::v2s_mt_posix::expressions::attribute_actor<boost::log::v2s_mt_posix::trivial::severity_level, boost::log::v2s_mt_posix::fallback_to_none, boost::log::v2s_mt_posix::trivial::tag::severity, boost::phoenix::actor> >, 2l> >, boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<char [3]>, 0l> > >, 2l> >, boost::log::v2s_mt_posix::expressions::attribute_actor<std::basic_string<char, std::char_traits<char>, std::allocator<char> >, boost::log::v2s_mt_posix::fallback_to_none, boost::log::v2s_mt_posix::expressions::tag::smessage, boost::phoenix::actor> >, 2l> > const> >(std::basic_ostream<char, std::char_traits<char> >&, boost::parameter::aux::tagged_argument<boost::log::v2s_mt_posix::keywords::tag::format, boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::shift_left, boost::proto::argsns_::list2<boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::shift_left, boost::proto::argsns_::list2<boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::shift_left, boost::proto::argsns_::list2<boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::shift_left, boost::proto::argsns_::list2<boost::phoenix::actor<boost::log::v2s_mt_posix::expressions::aux::attribute_output_terminal<boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<boost::phoenix::argument<2> >, 0l> >, boost::posix_time::ptime, boost::log::v2s_mt_posix::fallback_to_none, boost::log::v2s_mt_posix::aux::light_function<void (boost::log::v2s_mt_posix::basic_formatting_ostream<char, std::char_traits<char>, std::allocator<char> >&, boost::posix_time::ptime const&)> > >, boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<char [4]>, 0l> > >, 2l> >, boost::log::v2s_mt_posix::expressions::attribute_actor<boost::log::v2s_mt_posix::trivial::severity_level, boost::log::v2s_mt_posix::fallback_to_none, boost::log::v2s_mt_posix::trivial::tag::severity, boost::phoenix::actor> >, 2l> >, boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<char [3]>, 0l> > >, 2l> >, boost::log::v2s_mt_posix::expressions::attribute_actor<std::basic_string<char, std::char_traits<char>, std::allocator<char> >, boost::log::v2s_mt_posix::fallback_to_none, boost::log::v2s_mt_posix::expressions::tag::smessage, boost::phoenix::actor> >, 2l> > const> const&) in logger.cpp.o  
>   "boost::log::v2s_mt_posix::core::add_sink(boost::shared_ptr<boost::log::v2s_mt_posix::sinks::sink> const&)", referenced from:  
>       boost::shared_ptr<boost::log::v2s_mt_posix::sinks::synchronous_sink<boost::log::v2s_mt_posix::sinks::text_file_backend> > boost::log::v2s_mt_posix::aux::add_file_log<boost::parameter::aux::arg_list<boost::parameter::aux::tagged_argument<boost::log::v2s_mt_posix::keywords::tag::format, boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::shift_left, boost::proto::argsns_::list2<boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::shift_left, boost::proto::argsns_::list2<boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::shift_left, boost::proto::argsns_::list2<boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::shift_left, boost::proto::argsns_::list2<boost::phoenix::actor<boost::log::v2s_mt_posix::expressions::aux::attribute_output_terminal<boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<boost::phoenix::argument<2> >, 0l> >, boost::posix_time::ptime, boost::log::v2s_mt_posix::fallback_to_none, boost::log::v2s_mt_posix::aux::light_function<void (boost::log::v2s_mt_posix::basic_formatting_ostream<char, std::char_traits<char>, std::allocator<char> >&, boost::posix_time::ptime const&)> > >, boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<char [4]>, 0l> > >, 2l> >, boost::log::v2s_mt_posix::expressions::attribute_actor<boost::log::v2s_mt_posix::trivial::severity_level, boost::log::v2s_mt_posix::fallback_to_none, boost::log::v2s_mt_posix::trivial::tag::severity, boost::phoenix::actor> >, 2l> >, boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<char [3]>, 0l> > >, 2l> >, boost::log::v2s_mt_posix::expressions::attribute_actor<std::basic_string<char, std::char_traits<char>, std::allocator<char> >, boost::log::v2s_mt_posix::fallback_to_none, boost::log::v2s_mt_posix::expressions::tag::smessage, boost::phoenix::actor> >, 2l> > const>, boost::parameter::aux::arg_list<boost::parameter::aux::tagged_argument<boost::log::v2s_mt_posix::keywords::tag::time_based_rotation, boost::log::v2s_mt_posix::sinks::file::rotation_at_time_point const>, boost::parameter::aux::arg_list<boost::parameter::aux::tagged_argument<boost::log::v2s_mt_posix::keywords::tag::rotation_size, int const>, boost::parameter::aux::arg_list<boost::parameter::aux::tagged_argument<boost::log::v2s_mt_posix::keywords::tag::auto_flush, bool const>, boost::parameter::aux::arg_list<boost::parameter::aux::tagged_argument<boost::log::v2s_mt_posix::keywords::tag::file_name, std::basic_string<char, std::char_traits<char>, std::allocator<char> > const>, boost::parameter::aux::arg_list<boost::parameter::aux::tagged_argument<boost::log::v2s_mt_posix::keywords::tag::open_mode, std::_Ios_Openmode const>, boost::parameter::aux::empty_arg_list> > > > > > >(boost::parameter::aux::arg_list<boost::parameter::aux::tagged_argument<boost::log::v2s_mt_posix::keywords::tag::format, boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::shift_left, boost::proto::argsns_::list2<boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::shift_left, boost::proto::argsns_::list2<boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::shift_left, boost::proto::argsns_::list2<boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::shift_left, boost::proto::argsns_::list2<boost::phoenix::actor<boost::log::v2s_mt_posix::expressions::aux::attribute_output_terminal<boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<boost::phoenix::argument<2> >, 0l> >, boost::posix_time::ptime, boost::log::v2s_mt_posix::fallback_to_none, boost::log::v2s_mt_posix::aux::light_function<void (boost::log::v2s_mt_posix::basic_formatting_ostream<char, std::char_traits<char>, std::allocator<char> >&, boost::posix_time::ptime const&)> > >, boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<char [4]>, 0l> > >, 2l> >, boost::log::v2s_mt_posix::expressions::attribute_actor<boost::log::v2s_mt_posix::trivial::severity_level, boost::log::v2s_mt_posix::fallback_to_none, boost::log::v2s_mt_posix::trivial::tag::severity, boost::phoenix::actor> >, 2l> >, boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<char [3]>, 0l> > >, 2l> >, boost::log::v2s_mt_posix::expressions::attribute_actor<std::basic_string<char, std::char_traits<char>, std::allocator<char> >, boost::log::v2s_mt_posix::fallback_to_none, boost::log::v2s_mt_posix::expressions::tag::smessage, boost::phoenix::actor> >, 2l> > const>, boost::parameter::aux::arg_list<boost::parameter::aux::tagged_argument<boost::log::v2s_mt_posix::keywords::tag::time_based_rotation, boost::log::v2s_mt_posix::sinks::file::rotation_at_time_point const>, boost::parameter::aux::arg_list<boost::parameter::aux::tagged_argument<boost::log::v2s_mt_posix::keywords::tag::rotation_size, int const>, boost::parameter::aux::arg_list<boost::parameter::aux::tagged_argument<boost::log::v2s_mt_posix::keywords::tag::auto_flush, bool const>, boost::parameter::aux::arg_list<boost::parameter::aux::tagged_argument<boost::log::v2s_mt_posix::keywords::tag::file_name, std::basic_string<char, std::char_traits<char>, std::allocator<char> > const>, boost::parameter::aux::arg_list<boost::parameter::aux::tagged_argument<boost::log::v2s_mt_posix::keywords::tag::open_mode, std::_Ios_Openmode const>, boost::parameter::aux::empty_arg_list> > > > > > const&) in logger.cpp.o  
>       boost::shared_ptr<boost::log::v2s_mt_posix::sinks::synchronous_sink<boost::log::v2s_mt_posix::sinks::basic_text_ostream_backend<char> > > boost::log::v2s_mt_posix::aux::add_console_log<char, boost::parameter::aux::tagged_argument<boost::log::v2s_mt_posix::keywords::tag::format, boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::shift_left, boost::proto::argsns_::list2<boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::shift_left, boost::proto::argsns_::list2<boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::shift_left, boost::proto::argsns_::list2<boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::shift_left, boost::proto::argsns_::list2<boost::phoenix::actor<boost::log::v2s_mt_posix::expressions::aux::attribute_output_terminal<boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<boost::phoenix::argument<2> >, 0l> >, boost::posix_time::ptime, boost::log::v2s_mt_posix::fallback_to_none, boost::log::v2s_mt_posix::aux::light_function<void (boost::log::v2s_mt_posix::basic_formatting_ostream<char, std::char_traits<char>, std::allocator<char> >&, boost::posix_time::ptime const&)> > >, boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<char [4]>, 0l> > >, 2l> >, boost::log::v2s_mt_posix::expressions::attribute_actor<boost::log::v2s_mt_posix::trivial::severity_level, boost::log::v2s_mt_posix::fallback_to_none, boost::log::v2s_mt_posix::trivial::tag::severity, boost::phoenix::actor> >, 2l> >, boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<char [3]>, 0l> > >, 2l> >, boost::log::v2s_mt_posix::expressions::attribute_actor<std::basic_string<char, std::char_traits<char>, std::allocator<char> >, boost::log::v2s_mt_posix::fallback_to_none, boost::log::v2s_mt_posix::expressions::tag::smessage, boost::phoenix::actor> >, 2l> > const> >(std::basic_ostream<char, std::char_traits<char> >&, boost::parameter::aux::tagged_argument<boost::log::v2s_mt_posix::keywords::tag::format, boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::shift_left, boost::proto::argsns_::list2<boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::shift_left, boost::proto::argsns_::list2<boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::shift_left, boost::proto::argsns_::list2<boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::shift_left, boost::proto::argsns_::list2<boost::phoenix::actor<boost::log::v2s_mt_posix::expressions::aux::attribute_output_terminal<boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<boost::phoenix::argument<2> >, 0l> >, boost::posix_time::ptime, boost::log::v2s_mt_posix::fallback_to_none, boost::log::v2s_mt_posix::aux::light_function<void (boost::log::v2s_mt_posix::basic_formatting_ostream<char, std::char_traits<char>, std::allocator<char> >&, boost::posix_time::ptime const&)> > >, boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<char [4]>, 0l> > >, 2l> >, boost::log::v2s_mt_posix::expressions::attribute_actor<boost::log::v2s_mt_posix::trivial::severity_level, boost::log::v2s_mt_posix::fallback_to_none, boost::log::v2s_mt_posix::trivial::tag::severity, boost::phoenix::actor> >, 2l> >, boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<char [3]>, 0l> > >, 2l> >, boost::log::v2s_mt_posix::expressions::attribute_actor<std::basic_string<char, std::char_traits<char>, std::allocator<char> >, boost::log::v2s_mt_posix::fallback_to_none, boost::log::v2s_mt_posix::expressions::tag::smessage, boost::phoenix::actor> >, 2l> > const> const&) in logger.cpp.o  
>   "boost::log::v2s_mt_posix::sinks::text_file_backend::flush()", referenced from:  
>       boost::log::v2s_mt_posix::sinks::synchronous_sink<boost::log::v2s_mt_posix::sinks::text_file_backend>::flush() in logger.cpp.o  
>   "boost::log::v2s_mt_posix::sinks::text_file_backend::consume(boost::log::v2s_mt_posix::record_view const&, std::basic_string<char, std::char_traits<char>, std::allocator<char> > const&)", referenced from:  
>       boost::log::v2s_mt_posix::sinks::synchronous_sink<boost::log::v2s_mt_posix::sinks::text_file_backend>::try_consume(boost::log::v2s_mt_posix::record_view const&) in logger.cpp.o  
>       boost::log::v2s_mt_posix::sinks::synchronous_sink<boost::log::v2s_mt_posix::sinks::text_file_backend>::consume(boost::log::v2s_mt_posix::record_view const&) in logger.cpp.o  
>   "boost::log::v2s_mt_posix::sinks::text_file_backend::construct(boost::filesystem::path const&, std::_Ios_Openmode, unsigned long, boost::log::v2s_mt_posix::aux::light_function<bool ()> const&, bool, bool)", referenced from:  
>       boost::detail::sp_if_not_array<boost::log::v2s_mt_posix::sinks::text_file_backend>::type boost::make_shared<boost::log::v2s_mt_posix::sinks::text_file_backend, boost::parameter::aux::arg_list<boost::parameter::aux::tagged_argument<boost::log::v2s_mt_posix::keywords::tag::format, boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::shift_left, boost::proto::argsns_::list2<boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::shift_left, boost::proto::argsns_::list2<boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::shift_left, boost::proto::argsns_::list2<boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::shift_left, boost::proto::argsns_::list2<boost::phoenix::actor<boost::log::v2s_mt_posix::expressions::aux::attribute_output_terminal<boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<boost::phoenix::argument<2> >, 0l> >, boost::posix_time::ptime, boost::log::v2s_mt_posix::fallback_to_none, boost::log::v2s_mt_posix::aux::light_function<void (boost::log::v2s_mt_posix::basic_formatting_ostream<char, std::char_traits<char>, std::allocator<char> >&, boost::posix_time::ptime const&)> > >, boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<char [4]>, 0l> > >, 2l> >, boost::log::v2s_mt_posix::expressions::attribute_actor<boost::log::v2s_mt_posix::trivial::severity_level, boost::log::v2s_mt_posix::fallback_to_none, boost::log::v2s_mt_posix::trivial::tag::severity, boost::phoenix::actor> >, 2l> >, boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<char [3]>, 0l> > >, 2l> >, boost::log::v2s_mt_posix::expressions::attribute_actor<std::basic_string<char, std::char_traits<char>, std::allocator<char> >, boost::log::v2s_mt_posix::fallback_to_none, boost::log::v2s_mt_posix::expressions::tag::smessage, boost::phoenix::actor> >, 2l> > const>, boost::parameter::aux::arg_list<boost::parameter::aux::tagged_argument<boost::log::v2s_mt_posix::keywords::tag::time_based_rotation, boost::log::v2s_mt_posix::sinks::file::rotation_at_time_point const>, boost::parameter::aux::arg_list<boost::parameter::aux::tagged_argument<boost::log::v2s_mt_posix::keywords::tag::rotation_size, int const>, boost::parameter::aux::arg_list<boost::parameter::aux::tagged_argument<boost::log::v2s_mt_posix::keywords::tag::auto_flush, bool const>, boost::parameter::aux::arg_list<boost::parameter::aux::tagged_argument<boost::log::v2s_mt_posix::keywords::tag::file_name, std::basic_string<char, std::char_traits<char>, std::allocator<char> > const>, boost::parameter::aux::arg_list<boost::parameter::aux::tagged_argument<boost::log::v2s_mt_posix::keywords::tag::open_mode, std::_Ios_Openmode const>, boost::parameter::aux::empty_arg_list> > > > > > const&>(boost::parameter::aux::arg_list<boost::parameter::aux::tagged_argument<boost::log::v2s_mt_posix::keywords::tag::format, boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::shift_left, boost::proto::argsns_::list2<boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::shift_left, boost::proto::argsns_::list2<boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::shift_left, boost::proto::argsns_::list2<boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::shift_left, boost::proto::argsns_::list2<boost::phoenix::actor<boost::log::v2s_mt_posix::expressions::aux::attribute_output_terminal<boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<boost::phoenix::argument<2> >, 0l> >, boost::posix_time::ptime, boost::log::v2s_mt_posix::fallback_to_none, boost::log::v2s_mt_posix::aux::light_function<void (boost::log::v2s_mt_posix::basic_formatting_ostream<char, std::char_traits<char>, std::allocator<char> >&, boost::posix_time::ptime const&)> > >, boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<char [4]>, 0l> > >, 2l> >, boost::log::v2s_mt_posix::expressions::attribute_actor<boost::log::v2s_mt_posix::trivial::severity_level, boost::log::v2s_mt_posix::fallback_to_none, boost::log::v2s_mt_posix::trivial::tag::severity, boost::phoenix::actor> >, 2l> >, boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<char [3]>, 0l> > >, 2l> >, boost::log::v2s_mt_posix::expressions::attribute_actor<std::basic_string<char, std::char_traits<char>, std::allocator<char> >, boost::log::v2s_mt_posix::fallback_to_none, boost::log::v2s_mt_posix::expressions::tag::smessage, boost::phoenix::actor> >, 2l> > const>, boost::parameter::aux::arg_list<boost::parameter::aux::tagged_argument<boost::log::v2s_mt_posix::keywords::tag::time_based_rotation, boost::log::v2s_mt_posix::sinks::file::rotation_at_time_point const>, boost::parameter::aux::arg_list<boost::parameter::aux::tagged_argument<boost::log::v2s_mt_posix::keywords::tag::rotation_size, int const>, boost::parameter::aux::arg_list<boost::parameter::aux::tagged_argument<boost::log::v2s_mt_posix::keywords::tag::auto_flush, bool const>, boost::parameter::aux::arg_list<boost::parameter::aux::tagged_argument<boost::log::v2s_mt_posix::keywords::tag::file_name, std::basic_string<char, std::char_traits<char>, std::allocator<char> > const>, boost::parameter::aux::arg_list<boost::parameter::aux::tagged_argument<boost::log::v2s_mt_posix::keywords::tag::open_mode, std::_Ios_Openmode const>, boost::parameter::aux::empty_arg_list> > > > > > const&) in logger.cpp.o  
>   "boost::log::v2s_mt_posix::sinks::text_file_backend::~text_file_backend()", referenced from:  
>       boost::detail::sp_counted_impl_pd<boost::log::v2s_mt_posix::sinks::text_file_backend*, boost::detail::sp_ms_deleter<boost::log::v2s_mt_posix::sinks::text_file_backend> >::~sp_counted_impl_pd() in logger.cpp.o  
>       boost::detail::sp_counted_impl_pd<boost::log::v2s_mt_posix::sinks::text_file_backend*, boost::detail::sp_ms_deleter<boost::log::v2s_mt_posix::sinks::text_file_backend> >::~sp_counted_impl_pd() in logger.cpp.o  
>       boost::detail::sp_counted_impl_pd<boost::log::v2s_mt_posix::sinks::text_file_backend*, boost::detail::sp_ms_deleter<boost::log::v2s_mt_posix::sinks::text_file_backend> >::dispose() in logger.cpp.o  
>   "boost::log::v2s_mt_posix::sinks::basic_text_ostream_backend<char>::add_stream(boost::shared_ptr<std::basic_ostream<char, std::char_traits<char> > > const&)", referenced from:  
>       boost::shared_ptr<boost::log::v2s_mt_posix::sinks::synchronous_sink<boost::log::v2s_mt_posix::sinks::basic_text_ostream_backend<char> > > boost::log::v2s_mt_posix::aux::add_console_log<char, boost::parameter::aux::tagged_argument<boost::log::v2s_mt_posix::keywords::tag::format, boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::shift_left, boost::proto::argsns_::list2<boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::shift_left, boost::proto::argsns_::list2<boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::shift_left, boost::proto::argsns_::list2<boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::shift_left, boost::proto::argsns_::list2<boost::phoenix::actor<boost::log::v2s_mt_posix::expressions::aux::attribute_output_terminal<boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<boost::phoenix::argument<2> >, 0l> >, boost::posix_time::ptime, boost::log::v2s_mt_posix::fallback_to_none, boost::log::v2s_mt_posix::aux::light_function<void (boost::log::v2s_mt_posix::basic_formatting_ostream<char, std::char_traits<char>, std::allocator<char> >&, boost::posix_time::ptime const&)> > >, boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<char [4]>, 0l> > >, 2l> >, boost::log::v2s_mt_posix::expressions::attribute_actor<boost::log::v2s_mt_posix::trivial::severity_level, boost::log::v2s_mt_posix::fallback_to_none, boost::log::v2s_mt_posix::trivial::tag::severity, boost::phoenix::actor> >, 2l> >, boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<char [3]>, 0l> > >, 2l> >, boost::log::v2s_mt_posix::expressions::attribute_actor<std::basic_string<char, std::char_traits<char>, std::allocator<char> >, boost::log::v2s_mt_posix::fallback_to_none, boost::log::v2s_mt_posix::expressions::tag::smessage, boost::phoenix::actor> >, 2l> > const> >(std::basic_ostream<char, std::char_traits<char> >&, boost::parameter::aux::tagged_argument<boost::log::v2s_mt_posix::keywords::tag::format, boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::shift_left, boost::proto::argsns_::list2<boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::shift_left, boost::proto::argsns_::list2<boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::shift_left, boost::proto::argsns_::list2<boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::shift_left, boost::proto::argsns_::list2<boost::phoenix::actor<boost::log::v2s_mt_posix::expressions::aux::attribute_output_terminal<boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<boost::phoenix::argument<2> >, 0l> >, boost::posix_time::ptime, boost::log::v2s_mt_posix::fallback_to_none, boost::log::v2s_mt_posix::aux::light_function<void (boost::log::v2s_mt_posix::basic_formatting_ostream<char, std::char_traits<char>, std::allocator<char> >&, boost::posix_time::ptime const&)> > >, boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<char [4]>, 0l> > >, 2l> >, boost::log::v2s_mt_posix::expressions::attribute_actor<boost::log::v2s_mt_posix::trivial::severity_level, boost::log::v2s_mt_posix::fallback_to_none, boost::log::v2s_mt_posix::trivial::tag::severity, boost::phoenix::actor> >, 2l> >, boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<char [3]>, 0l> > >, 2l> >, boost::log::v2s_mt_posix::expressions::attribute_actor<std::basic_string<char, std::char_traits<char>, std::allocator<char> >, boost::log::v2s_mt_posix::fallback_to_none, boost::log::v2s_mt_posix::expressions::tag::smessage, boost::phoenix::actor> >, 2l> > const> const&) in logger.cpp.o  
>   "boost::log::v2s_mt_posix::sinks::basic_text_ostream_backend<char>::auto_flush(bool)", referenced from:  
>       boost::shared_ptr<boost::log::v2s_mt_posix::sinks::synchronous_sink<boost::log::v2s_mt_posix::sinks::basic_text_ostream_backend<char> > > boost::log::v2s_mt_posix::aux::add_console_log<char, boost::parameter::aux::tagged_argument<boost::log::v2s_mt_posix::keywords::tag::format, boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::shift_left, boost::proto::argsns_::list2<boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::shift_left, boost::proto::argsns_::list2<boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::shift_left, boost::proto::argsns_::list2<boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::shift_left, boost::proto::argsns_::list2<boost::phoenix::actor<boost::log::v2s_mt_posix::expressions::aux::attribute_output_terminal<boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<boost::phoenix::argument<2> >, 0l> >, boost::posix_time::ptime, boost::log::v2s_mt_posix::fallback_to_none, boost::log::v2s_mt_posix::aux::light_function<void (boost::log::v2s_mt_posix::basic_formatting_ostream<char, std::char_traits<char>, std::allocator<char> >&, boost::posix_time::ptime const&)> > >, boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<char [4]>, 0l> > >, 2l> >, boost::log::v2s_mt_posix::expressions::attribute_actor<boost::log::v2s_mt_posix::trivial::severity_level, boost::log::v2s_mt_posix::fallback_to_none, boost::log::v2s_mt_posix::trivial::tag::severity, boost::phoenix::actor> >, 2l> >, boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<char [3]>, 0l> > >, 2l> >, boost::log::v2s_mt_posix::expressions::attribute_actor<std::basic_string<char, std::char_traits<char>, std::allocator<char> >, boost::log::v2s_mt_posix::fallback_to_none, boost::log::v2s_mt_posix::expressions::tag::smessage, boost::phoenix::actor> >, 2l> > const> >(std::basic_ostream<char, std::char_traits<char> >&, boost::parameter::aux::tagged_argument<boost::log::v2s_mt_posix::keywords::tag::format, boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::shift_left, boost::proto::argsns_::list2<boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::shift_left, boost::proto::argsns_::list2<boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::shift_left, boost::proto::argsns_::list2<boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::shift_left, boost::proto::argsns_::list2<boost::phoenix::actor<boost::log::v2s_mt_posix::expressions::aux::attribute_output_terminal<boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<boost::phoenix::argument<2> >, 0l> >, boost::posix_time::ptime, boost::log::v2s_mt_posix::fallback_to_none, boost::log::v2s_mt_posix::aux::light_function<void (boost::log::v2s_mt_posix::basic_formatting_ostream<char, std::char_traits<char>, std::allocator<char> >&, boost::posix_time::ptime const&)> > >, boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<char [4]>, 0l> > >, 2l> >, boost::log::v2s_mt_posix::expressions::attribute_actor<boost::log::v2s_mt_posix::trivial::severity_level, boost::log::v2s_mt_posix::fallback_to_none, boost::log::v2s_mt_posix::trivial::tag::severity, boost::phoenix::actor> >, 2l> >, boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<char [3]>, 0l> > >, 2l> >, boost::log::v2s_mt_posix::expressions::attribute_actor<std::basic_string<char, std::char_traits<char>, std::allocator<char> >, boost::log::v2s_mt_posix::fallback_to_none, boost::log::v2s_mt_posix::expressions::tag::smessage, boost::phoenix::actor> >, 2l> > const> const&) in logger.cpp.o  
>   "boost::log::v2s_mt_posix::sinks::basic_text_ostream_backend<char>::flush()", referenced from:  
>       boost::log::v2s_mt_posix::sinks::synchronous_sink<boost::log::v2s_mt_posix::sinks::basic_text_ostream_backend<char> >::flush() in logger.cpp.o  
>   "boost::log::v2s_mt_posix::sinks::basic_text_ostream_backend<char>::consume(boost::log::v2s_mt_posix::record_view const&, std::basic_string<char, std::char_traits<char>, std::allocator<char> > const&)", referenced from:  
>       boost::log::v2s_mt_posix::sinks::synchronous_sink<boost::log::v2s_mt_posix::sinks::basic_text_ostream_backend<char> >::consume(boost::log::v2s_mt_posix::record_view const&) in logger.cpp.o  
>       boost::log::v2s_mt_posix::sinks::synchronous_sink<boost::log::v2s_mt_posix::sinks::basic_text_ostream_backend<char> >::try_consume(boost::log::v2s_mt_posix::record_view const&) in logger.cpp.o  
>   "boost::log::v2s_mt_posix::sinks::basic_text_ostream_backend<char>::basic_text_ostream_backend()", referenced from:  
>       boost::detail::sp_if_not_array<boost::log::v2s_mt_posix::sinks::basic_text_ostream_backend<char> >::type boost::make_shared<boost::log::v2s_mt_posix::sinks::basic_text_ostream_backend<char>>() in logger.cpp.o  
>   "boost::log::v2s_mt_posix::sinks::basic_text_ostream_backend<char>::~basic_text_ostream_backend()", referenced from:  
>       boost::detail::sp_counted_impl_pd<boost::log::v2s_mt_posix::sinks::basic_text_ostream_backend<char>*, boost::detail::sp_ms_deleter<boost::log::v2s_mt_posix::sinks::basic_text_ostream_backend<char> > >::~sp_counted_impl_pd() in logger.cpp.o  
>       boost::detail::sp_counted_impl_pd<boost::log::v2s_mt_posix::sinks::basic_text_ostream_backend<char>*, boost::detail::sp_ms_deleter<boost::log::v2s_mt_posix::sinks::basic_text_ostream_backend<char> > >::~sp_counted_impl_pd() in logger.cpp.o  
>       boost::detail::sp_counted_impl_pd<boost::log::v2s_mt_posix::sinks::basic_text_ostream_backend<char>*, boost::detail::sp_ms_deleter<boost::log::v2s_mt_posix::sinks::basic_text_ostream_backend<char> > >::dispose() in logger.cpp.o  
>   "boost::log::v2s_mt_posix::sinks::file::rotation_at_time_point::rotation_at_time_point(unsigned char, unsigned char, unsigned char)", referenced from:  
>       init_framework_logging(std::basic_string<char, std::char_traits<char>, std::allocator<char> > const&) in logger.cpp.o  
>   "boost::log::v2s_mt_posix::trivial::to_string(boost::log::v2s_mt_posix::trivial::severity_level)", referenced from:  
>       boost::log::v2s_mt_posix::aux::light_function<void (boost::log::v2s_mt_posix::record_view const&, boost::log::v2s_mt_posix::expressions::aux::stream_ref<boost::log::v2s_mt_posix::basic_formatting_ostream<char, std::char_traits<char>, std::allocator<char> > >)>::impl<boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::shift_left, boost::proto::argsns_::list2<boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::shift_left, boost::proto::argsns_::list2<boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::shift_left, boost::proto::argsns_::list2<boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::shift_left, boost::proto::argsns_::list2<boost::phoenix::actor<boost::log::v2s_mt_posix::expressions::aux::attribute_output_terminal<boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<boost::phoenix::argument<2> >, 0l> >, boost::posix_time::ptime, boost::log::v2s_mt_posix::fallback_to_none, boost::log::v2s_mt_posix::aux::light_function<void (boost::log::v2s_mt_posix::basic_formatting_ostream<char, std::char_traits<char>, std::allocator<char> >&, boost::posix_time::ptime const&)> > >, boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<char [4]>, 0l> > >, 2l> >, boost::log::v2s_mt_posix::expressions::attribute_actor<boost::log::v2s_mt_posix::trivial::severity_level, boost::log::v2s_mt_posix::fallback_to_none, boost::log::v2s_mt_posix::trivial::tag::severity, boost::phoenix::actor> >, 2l> >, boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<char [3]>, 0l> > >, 2l> >, boost::log::v2s_mt_posix::expressions::attribute_actor<std::basic_string<char, std::char_traits<char>, std::allocator<char> >, boost::log::v2s_mt_posix::fallback_to_none, boost::log::v2s_mt_posix::expressions::tag::smessage, boost::phoenix::actor> >, 2l> > >::invoke_impl(void*, boost::log::v2s_mt_posix::record_view const&, boost::log::v2s_mt_posix::expressions::aux::stream_ref<boost::log::v2s_mt_posix::basic_formatting_ostream<char, std::char_traits<char>, std::allocator<char> > >) in logger.cpp.o  
>   "boost::log::v2s_mt_posix::attribute::impl::operator delete(void*, unsigned long)", referenced from:  
>       boost::log::v2s_mt_posix::attributes::basic_clock<boost::log::v2s_mt_posix::attributes::local_time_traits>::impl::~impl() in logger.cpp.o  
>       boost::log::v2s_mt_posix::attributes::counter<unsigned int>::impl::~impl() in logger.cpp.o  
>       boost::log::v2s_mt_posix::attributes::attribute_value_impl<boost::posix_time::ptime>::~attribute_value_impl() in logger.cpp.o  
>       boost::log::v2s_mt_posix::attributes::attribute_value_impl<unsigned int>::~attribute_value_impl() in logger.cpp.o  
>       boost::log::v2s_mt_posix::attributes::current_thread_id::impl::~impl() in logger.cpp.o  
>       boost::log::v2s_mt_posix::attributes::attribute_value_impl<boost::log::v2s_mt_posix::aux::id<boost::log::v2s_mt_posix::aux::thread> >::~attribute_value_impl() in logger.cpp.o  
>       boost::log::v2s_mt_posix::attributes::attribute_value_impl<boost::log::v2s_mt_posix::aux::id<boost::log::v2s_mt_posix::aux::process> >::~attribute_value_impl() in logger.cpp.o  
>       ...  
>   "boost::log::v2s_mt_posix::attribute::impl::operator new(unsigned long)", referenced from:  
>       boost::log::v2s_mt_posix::attributes::current_thread_id::impl::detach_from_thread()    in logger.cpp.o  
>       boost::log::v2s_mt_posix::attributes::counter<unsigned int>::impl::get_value() in logger.cpp.o  
>       boost::log::v2s_mt_posix::add_common_attributes()     in logger.cpp.o  
>       boost::log::v2s_mt_posix::attributes::basic_clock<boost::log::v2s_mt_posix::attributes::local_time_traits>::impl::get_value() in logger.cpp.o  
>   "boost::log::v2s_mt_posix::attribute_value_set::end() const", referenced from:  
>       boost::log::v2s_mt_posix::aux::light_function<bool (boost::log::v2s_mt_posix::attribute_value_set const&)>::impl<boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::greater_equal, boost::proto::argsns_::list2<boost::log::v2s_mt_posix::expressions::attribute_actor<boost::log::v2s_mt_posix::trivial::severity_level, boost::log::v2s_mt_posix::fallback_to_none, boost::log::v2s_mt_posix::trivial::tag::severity, boost::phoenix::actor>, boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<boost::log::v2s_mt_posix::trivial::severity_level>, 0l> > >, 2l> > >::invoke_impl(void*, boost::log::v2s_mt_posix::attribute_value_set const&) in logger.cpp.o  
>       boost::log::v2s_mt_posix::value_extractor<boost::log::v2s_mt_posix::trivial::severity_level, boost::log::v2s_mt_posix::fallback_to_none, boost::log::v2s_mt_posix::trivial::tag::severity>::operator()(boost::log::v2s_mt_posix::attribute_name const&, boost::log::v2s_mt_posix::attribute_value_set const&) const in logger.cpp.o  
>       boost::log::v2s_mt_posix::aux::light_function<void (boost::log::v2s_mt_posix::record_view const&, boost::log::v2s_mt_posix::expressions::aux::stream_ref<boost::log::v2s_mt_posix::basic_formatting_ostream<char, std::char_traits<char>, std::allocator<char> > >)>::impl<boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::shift_left, boost::proto::argsns_::list2<boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::shift_left, boost::proto::argsns_::list2<boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::shift_left, boost::proto::argsns_::list2<boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::shift_left, boost::proto::argsns_::list2<boost::phoenix::actor<boost::log::v2s_mt_posix::expressions::aux::attribute_output_terminal<boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<boost::phoenix::argument<2> >, 0l> >, boost::posix_time::ptime, boost::log::v2s_mt_posix::fallback_to_none, boost::log::v2s_mt_posix::aux::light_function<void (boost::log::v2s_mt_posix::basic_formatting_ostream<char, std::char_traits<char>, std::allocator<char> >&, boost::posix_time::ptime const&)> > >, boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<char [4]>, 0l> > >, 2l> >, boost::log::v2s_mt_posix::expressions::attribute_actor<boost::log::v2s_mt_posix::trivial::severity_level, boost::log::v2s_mt_posix::fallback_to_none, boost::log::v2s_mt_posix::trivial::tag::severity, boost::phoenix::actor> >, 2l> >, boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<char [3]>, 0l> > >, 2l> >, boost::log::v2s_mt_posix::expressions::attribute_actor<std::basic_string<char, std::char_traits<char>, std::allocator<char> >, boost::log::v2s_mt_posix::fallback_to_none, boost::log::v2s_mt_posix::expressions::tag::smessage, boost::phoenix::actor> >, 2l> > >::invoke_impl(void*, boost::log::v2s_mt_posix::record_view const&, boost::log::v2s_mt_posix::expressions::aux::stream_ref<boost::log::v2s_mt_posix::basic_formatting_ostream<char, std::char_traits<char>, std::allocator<char> > >) in logger.cpp.o  
>       boost::log::v2s_mt_posix::aux::light_function<void (boost::log::v2s_mt_posix::record_view const&, boost::log::v2s_mt_posix::expressions::aux::stream_ref<boost::log::v2s_mt_posix::basic_formatting_ostream<char, std::char_traits<char>, std::allocator<char> > >)>::impl<boost::log::v2s_mt_posix::expressions::aux::message_formatter>::invoke_impl(void*, boost::log::v2s_mt_posix::record_view const&, boost::log::v2s_mt_posix::expressions::aux::stream_ref<boost::log::v2s_mt_posix::basic_formatting_ostream<char, std::char_traits<char>, std::allocator<char> > >) in logger.cpp.o  
>   "boost::log::v2s_mt_posix::attribute_value_set::find(boost::log::v2s_mt_posix::attribute_name) const", referenced from:  
>       boost::log::v2s_mt_posix::aux::light_function<bool (boost::log::v2s_mt_posix::attribute_value_set const&)>::impl<boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::greater_equal, boost::proto::argsns_::list2<boost::log::v2s_mt_posix::expressions::attribute_actor<boost::log::v2s_mt_posix::trivial::severity_level, boost::log::v2s_mt_posix::fallback_to_none, boost::log::v2s_mt_posix::trivial::tag::severity, boost::phoenix::actor>, boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<boost::log::v2s_mt_posix::trivial::severity_level>, 0l> > >, 2l> > >::invoke_impl(void*, boost::log::v2s_mt_posix::attribute_value_set const&) in logger.cpp.o  
>       boost::log::v2s_mt_posix::value_extractor<boost::log::v2s_mt_posix::trivial::severity_level, boost::log::v2s_mt_posix::fallback_to_none, boost::log::v2s_mt_posix::trivial::tag::severity>::operator()(boost::log::v2s_mt_posix::attribute_name const&, boost::log::v2s_mt_posix::attribute_value_set const&) const in logger.cpp.o  
>       boost::log::v2s_mt_posix::aux::light_function<void (boost::log::v2s_mt_posix::record_view const&, boost::log::v2s_mt_posix::expressions::aux::stream_ref<boost::log::v2s_mt_posix::basic_formatting_ostream<char, std::char_traits<char>, std::allocator<char> > >)>::impl<boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::shift_left, boost::proto::argsns_::list2<boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::shift_left, boost::proto::argsns_::list2<boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::shift_left, boost::proto::argsns_::list2<boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::shift_left, boost::proto::argsns_::list2<boost::phoenix::actor<boost::log::v2s_mt_posix::expressions::aux::attribute_output_terminal<boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<boost::phoenix::argument<2> >, 0l> >, boost::posix_time::ptime, boost::log::v2s_mt_posix::fallback_to_none, boost::log::v2s_mt_posix::aux::light_function<void (boost::log::v2s_mt_posix::basic_formatting_ostream<char, std::char_traits<char>, std::allocator<char> >&, boost::posix_time::ptime const&)> > >, boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<char [4]>, 0l> > >, 2l> >, boost::log::v2s_mt_posix::expressions::attribute_actor<boost::log::v2s_mt_posix::trivial::severity_level, boost::log::v2s_mt_posix::fallback_to_none, boost::log::v2s_mt_posix::trivial::tag::severity, boost::phoenix::actor> >, 2l> >, boost::phoenix::actor<boost::proto::exprns_::basic_expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<char [3]>, 0l> > >, 2l> >, boost::log::v2s_mt_posix::expressions::attribute_actor<std::basic_string<char, std::char_traits<char>, std::allocator<char> >, boost::log::v2s_mt_posix::fallback_to_none, boost::log::v2s_mt_posix::expressions::tag::smessage, boost::phoenix::actor> >, 2l> > >::invoke_impl(void*, boost::log::v2s_mt_posix::record_view const&, boost::log::v2s_mt_posix::expressions::aux::stream_ref<boost::log::v2s_mt_posix::basic_formatting_ostream<char, std::char_traits<char>, std::allocator<char> > >) in logger.cpp.o  
>       boost::log::v2s_mt_posix::aux::light_function<void (boost::log::v2s_mt_posix::record_view const&, boost::log::v2s_mt_posix::expressions::aux::stream_ref<boost::log::v2s_mt_posix::basic_formatting_ostream<char, std::char_traits<char>, std::allocator<char> > >)>::impl<boost::log::v2s_mt_posix::expressions::aux::message_formatter>::invoke_impl(void*, boost::log::v2s_mt_posix::record_view const&, boost::log::v2s_mt_posix::expressions::aux::stream_ref<boost::log::v2s_mt_posix::basic_formatting_ostream<char, std::char_traits<char>, std::allocator<char> > >) in logger.cpp.o  
>   "boost::log::v2s_mt_posix::aux::once_block_sentry::enter_once_block() const", referenced from:  
>       boost::array<std::pair<boost::typeindex::stl_type_index, void*>, 2ul> const& boost::log::v2s_mt_posix::aux::type_sequence_dispatcher<boost::mpl::vector2<std::basic_string<char, std::char_traits<char>, std::allocator<char> >, std::basic_string<wchar_t, std::char_traits<wchar_t>, std::allocator<wchar_t> > > >::get_dispatching_map<boost::log::v2s_mt_posix::binder1st<boost::log::v2s_mt_posix::output_fun, boost::log::v2s_mt_posix::expressions::aux::stream_ref<boost::log::v2s_mt_posix::basic_formatting_ostream<char, std::char_traits<char>, std::allocator<char> > >&> >() in logger.cpp.o  
>   "boost::log::v2s_mt_posix::sinks::file::rotation_at_time_point::operator()() const", referenced from:  
>       boost::log::v2s_mt_posix::aux::light_function<bool ()>::impl<boost::log::v2s_mt_posix::sinks::file::rotation_at_time_point>::invoke_impl(void*) in logger.cpp.o  
> ld: symbol(s) not found for architecture x86_64  
> collect2: error: ld returned 1 exit status

---

## Comment 1

> Username: bkda  
> Created at: 2017-12-27 01:56:09 UTC  
> Url: https://github.com/boostorg/log/issues/46#issuecomment-354036439  

Need your help,many thanks.

---

## Comment 2

> Username: Lastique  
> Created at: 2017-12-27 06:21:44 UTC  
> Url: https://github.com/boostorg/log/issues/46#issuecomment-354059893  

The problem is likely mismatch between the library you're compiling for and your linker selects. `v2s_mt_posix` means you're compiling for the static library of Boost.Log and the linker is probably using the shared library by default.  
  
If you want to link with the static library, you should explicitly specify it with the full path and no `-l` switch in the command line (e.g. `gcc foo.o /usr/lib/libboost_log.a`) or make sure no shared library is found by the linker. I don't know if CMake offers an easy way to do this.  
  
If you want to link with the shared library then you should define `BOOST_LOG_DYN_LINK` or `BOOST_ALL_DYN_LINK`. See [here](http://www.boost.org/doc/libs/1_66_0/libs/log/doc/html/log/rationale/namespace_mangling.html).

---

## Comment 3

> Username: bkda  
> Created at: 2017-12-27 09:14:23 UTC  
> Updated at: 2017-12-27 09:15:20 UTC  
> Url: https://github.com/boostorg/log/issues/46#issuecomment-354081509  

I tried many ways, even specify full path, `/usr/local/lib/libboost_log_setup.a /usr/local/lib/libboost_log.a` still don't work.  
  
It wastes me too much time.

---

## Comment 4

> Username: Lastique  
> Created at: 2017-12-27 10:07:46 UTC  
> Url: https://github.com/boostorg/log/issues/46#issuecomment-354089561  

Does the linker issue any errors, like when it can't find the libraries you specify? If not, can you see the symbol names in the static libraries? In particular, the namespace name in the symbols is of interest. Using the link I posted, you can see the difference in the namespace names and adjust your config accordingly.

---

## Comment 5

> Username: bkda  
> Created at: 2017-12-27 10:22:02 UTC  
> Updated at: 2017-12-27 10:22:22 UTC  
> Url: https://github.com/boostorg/log/issues/46#issuecomment-354091586  

No, I can't see any errors, it just tells me `Undefined symbols for architecture x86_64`.  
  
I added `add_definitions(-DBOOST_LOG_DYN_LINK)` into cmakelists.txt and it works on linux but on my mac ,it still doesn't work.

---

## Comment 6

> Username: Lastique  
> Created at: 2017-12-27 12:49:21 UTC  
> Url: https://github.com/boostorg/log/issues/46#issuecomment-354109436  

Sorry, but I can't suggest anything helpful except to inspect your static libraries to see what's wrong with them. As a wild guess, is it possible that the libraries are 32-bit?  
  
You can also try building Boost yourself.

---

## Comment 7

> Username: enhering  
> Created at: 2019-08-13 02:22:24 UTC  
> Url: https://github.com/boostorg/log/issues/46#issuecomment-520663151  

Same on my MAC. Tried all solutions to fix it, but could not. I'm using Sierra and Boost 1.70.

---

## Comment 8

> Username: Divyank004  
> Created at: 2020-03-23 09:31:54 UTC  
> Url: https://github.com/boostorg/log/issues/46#issuecomment-602481649  

Is anyone able to find the solution for the above issue in MAC

---

## Comment 9

> Username: amosialek  
> Created at: 2020-03-26 16:57:48 UTC  
> Updated at: 2020-03-26 16:59:09 UTC  
> Url: https://github.com/boostorg/log/issues/46#issuecomment-604547598  

I've added `add_definitions(-DBOOST_LOG_DYN_LINK)` and still getting the same error.   
BOOST 1.71.0  
Ubuntu 18.04.3  
  
CMakeLists:  
  
`  
cmake_minimum_required(VERSION 3.10.0)  
  
add_executable(Graphessor main.cpp)  
find_package(Threads REQUIRED)  
target_link_libraries (Graphessor Threads::Threads)  
add_definitions(-DBOOST_LOG_DYN_LINK)  
target_link_libraries (Graphessor Models)  
target_link_libraries (Graphessor IO)  
target_link_libraries (Graphessor Logic)  
find_package(Boost COMPONENTS program_options REQUIRED)  
INCLUDE_DIRECTORIES( ${Boost_INCLUDE_DIR} )  
TARGET_LINK_LIBRARIES( Graphessor ${Boost_LIBRARIES} )  
`

---

## Comment 10

> Username: sflis  
> Created at: 2020-05-23 15:57:55 UTC  
> Url: https://github.com/boostorg/log/issues/46#issuecomment-633082193  

For any of you that have these issues when compiling against static boost libraries the issue is actually that the FindBoost module in cmake is broken (see https://gitlab.kitware.com/cmake/cmake/-/issues/20638). The dependencies set for Boost::log are wrong in the find module as Boost::log_setup depends on Boost::log not the other way around.   
  
Unfortunately I haven't figured out a clean workaround for this. Adding the libraries (not the targets) manually in the correct order solves the linker error, however it screws up the cmake  configuration export.

---

## Comment 11

> Username: a-r-db  
> Created at: 2020-07-07 18:33:40 UTC  
> Url: https://github.com/boostorg/log/issues/46#issuecomment-655046713  

The following worked for me.  
```cmake  
cmake_minimum_required(VERSION 3.16)  
project(PROJECT_NAME)  
set(CMAKE_CXX_STANDARD 20)  
  
find_package(Boost 1.71 REQUIRED COMPONENTS log_setup log)  
add_executable(PROJECT_NAME main.cpp)  
  
target_link_libraries(PROJECT_NAME ${Boost_LIBRARIES})  
```

---

## Comment 12

> Username: ghost  
> Created at: 2020-08-13 17:32:36 UTC  
> Url: https://github.com/boostorg/log/issues/46#issuecomment-673609962  

@a93h 's solution didn't work for me.  But this did the trick:  
  
```cmake  
find_package(Boost 1.73 REQUIRED COMPONENTS log)  
find_library(loglib libboost_log.a 3rdparty/boost_1_73_0/stage/lib)  
target_link_libraries(Boost::log INTERFACE ${loglib})  
```  
This adds an additional instance of libboost_log at the end of the compiler line when linking.    
Obviously change `libboost_log.a` to `boost_log` if you want the shared library version, and put your own path to the boost libraries dir.

---

## Comment 13

> Username: adityaprakash75  
> Created at: 2023-01-10 11:43:26 UTC  
> Url: https://github.com/boostorg/log/issues/46#issuecomment-1377131487  

Anyone got any solid fix for this on MAC??

---

## Comment 14

> Username: DreadTrader  
> Created at: 2023-10-15 08:44:28 UTC  
> Url: https://github.com/boostorg/log/issues/46#issuecomment-1763323372  

https://www.boost.org/doc/libs/1_54_0/libs/log/doc/html/log/rationale/namespace_mangling.html  
  
read this article  
  
before using any boost.log lib you need to define   
https://www.boost.org/doc/libs/1_54_0/libs/log/doc/html/log/installation/config.html

---

## Comment 15

> Username: marek22k  
> Created at: 2024-03-05 17:41:58 UTC  
> Updated at: 2024-03-05 17:44:46 UTC  
> Url: https://github.com/boostorg/log/issues/46#issuecomment-1979305164  

How do I integrate boost log static into Meson?  
  
```meson  
boost_dep = dependency('boost', modules : ['log'])  
```

---

## Comment 16

> Username: Lastique  
> Created at: 2024-03-05 21:02:27 UTC  
> Url: https://github.com/boostorg/log/issues/46#issuecomment-1979630275  

Sorry, I never used Meson.

---

## Comment 17

> Username: Raindragon-abc  
> Created at: 2025-11-04 12:21:35 UTC  
> Url: https://github.com/boostorg/log/issues/46#issuecomment-3485710522  

i meet the same problem at MacOS   
the following methods  is work for me  
  
`        target_compile_definitions(myproject PUBLIC BOOST_LOG_DYN_LINK BOOST_ALL_DYN_LINK)  
`  
i find the solution at   
https://www.boost.org/doc/libs/1_66_0/libs/log/doc/html/log/rationale/namespace_mangling.html  
and here is the detail of BOOST_LOG_DYN_LINK and BOOST_ALL_DYN_LINK  
https://www.boost.org/doc/libs/1_54_0/libs/log/doc/html/log/installation/config.html
