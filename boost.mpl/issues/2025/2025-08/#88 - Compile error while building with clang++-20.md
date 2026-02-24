# #88 - Compile error while building with clang++-20 [Open]

> Username: vikk777  
> Created at: 2025-08-07 12:15:04 UTC  
> Updated at: 2026-02-14 21:41:35 UTC  
> Url: https://github.com/boostorg/mpl/issues/88  

I build the ceph project which included boost and compiled it from sources.  
While building I encountered with compile error - non-type template argument is not a constant expression.  
I use the clang++-20 compiler on Astra Linux 1.8 (Debian 13).  
Note: compilation with clang++-14 was done without errors.  
Can you fix this error for support clang > 14, please?  
  
Build command:  
```  
cd /ceph-inst/build/src/rgw && clang++ -I/ceph-inst/src/boost -DBOOST_ALL_NO_LIB -DBOOST_ASIO_DISABLE_THREAD_KEYWORD_EXTENSION -DBOOST_ASIO_USE_TS_EXECUTOR_AS_DEFAULT -DBOOST_ATOMIC_DYN_LINK -DBOOST_CONTEXT_DYN_LINK -DBOOST_DATE_TIME_DYN_LINK -DBOOST_FILESYSTEM_DYN_LINK -DBOOST_IOSTREAMS_DYN_LINK -DBOOST_PROGRAM_OPTIONS_DYN_LINK -DBOOST_RANDOM_DYN_LINK -DBOOST_SYSTEM_DYN_LINK -DBOOST_THREAD_DYN_LINK -DFMT_DEPRECATED_OSTREAM -DHAVE_CONFIG_H -D_FILE_OFFSET_BITS=64 -D_GNU_SOURCE -D_REENTRANT -D_THREAD_SAFE -D__CEPH__ -D__STDC_FORMAT_MACROS -D__linux__ -I/ceph-inst/build/src/include -I/ceph-inst/src -I/ceph-inst/src/dmclock/src -I/ceph-inst/src/dmclock/support/src -I/ceph-inst/src/spawn/include -isystem /ceph-inst/build/include -isystem /ceph-inst/src/xxHash -isystem /ceph-inst/src/rapidjson/include -isystem /ceph-inst/src/fmt/include -isystem /ceph-inst/src/rgw/services -isystem /ceph-inst/src/rgw -isystem /usr/include/lua5.3 -g -O0 -fPIC -fno-builtin-malloc -fno-builtin-calloc -fno-builtin-realloc -fno-builtin-free -Wall -fno-strict-aliasing -fsigned-char -Wtype-limits -Wignored-qualifiers -Wpointer-arith -Werror=format-security -Winit-self -Wno-unknown-pragmas -Wnon-virtual-dtor -Wno-ignored-qualifiers -ftemplate-depth-1024 -Wpessimizing-move -Wredundant-move -fdiagnostics-color=auto -std=c++17 -MD -MT src/rgw/CMakeFiles/rgw_common.dir/rgw_s3select.cc.o -MF CMakeFiles/rgw_common.dir/rgw_s3select.cc.o.d -o /rgw_s3select.cc.o -c /ceph-inst/src/rgw/rgw_s3select.cc  
```  
  
Error output:  
```cpp  
In file included from /ceph-inst/src/rgw/rgw_s3select.cc:4:  
In file included from /ceph-inst/src/rgw/rgw_s3select_private.h:11:  
In file included from /ceph-inst/src/common/ceph_crypto.h:10:  
In file included from /ceph-inst/src/include/types.h:21:  
In file included from /ceph-inst/src/include/uuid.h:9:  
In file included from /ceph-inst/src/include/encoding.h:41:  
In file included from /ceph-inst/src/include/denc.h:51:  
In file included from /ceph-inst/src/common/error_code.h:22:  
In file included from /ceph-inst/src/boost/boost/asio.hpp:25:  
In file included from /ceph-inst/src/boost/boost/asio/basic_deadline_timer.hpp:25:  
In file included from /ceph-inst/src/boost/boost/asio/detail/deadline_timer_service.hpp:29:  
In file included from /ceph-inst/src/boost/boost/asio/detail/timer_queue_ptime.hpp:22:  
In file included from /ceph-inst/src/boost/boost/asio/time_traits.hpp:23:  
In file included from /ceph-inst/src/boost/boost/date_time/posix_time/posix_time_types.hpp:16:  
In file included from /ceph-inst/src/boost/boost/date_time/posix_time/posix_time_duration.hpp:15:  
In file included from /ceph-inst/src/boost/boost/numeric/conversion/cast.hpp:33:  
In file included from /ceph-inst/src/boost/boost/numeric/conversion/converter.hpp:13:  
In file included from /ceph-inst/src/boost/boost/numeric/conversion/conversion_traits.hpp:13:  
In file included from /ceph-inst/src/boost/boost/numeric/conversion/detail/conversion_traits.hpp:18:  
In file included from /ceph-inst/src/boost/boost/numeric/conversion/detail/int_float_mixture.hpp:19:  
In file included from /ceph-inst/src/boost/boost/mpl/integral_c.hpp:32:  
/ceph-inst/src/boost/boost/mpl/aux_/integral_wrapper.hpp:73:31: error: non-type template argument is not a constant expression  
   73 |     typedef AUX_WRAPPER_INST( BOOST_MPL_AUX_STATIC_CAST(AUX_WRAPPER_VALUE_TYPE, (value - 1)) ) prior;  
      |             ~~~~~~~~~~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
/ceph-inst/src/boost/boost/mpl/aux_/static_cast.hpp:24:47: note: expanded from macro 'BOOST_MPL_AUX_STATIC_CAST'  
   24 | #   define BOOST_MPL_AUX_STATIC_CAST(T, expr) static_cast<T>(expr)  
      |                                               ^  
/ceph-inst/src/boost/boost/mpl/integral_c.hpp:31:54: note: expanded from macro 'AUX_WRAPPER_INST'  
   31 | #define AUX_WRAPPER_INST(value) AUX_WRAPPER_NAME< T, value >  
      |                                                      ^~~~~  
/ceph-inst/src/boost/boost/numeric/conversion/detail/meta.hpp:30:46: note: in instantiation of template class 'mpl_::integral_c<boost::numeric::udt_builtin_mixture_enum, boost::numeric::builtin_to_builtin>' requested here  
   30 |        enum { x = ( BOOST_MPL_AUX_VALUE_WKND(T1)::value == BOOST_MPL_AUX_VALUE_WKND(T2)::value ) };  
      |                                              ^  
/ceph-inst/src/boost/boost/mpl/if.hpp:63:68: note: in instantiation of template class 'boost::numeric::convdetail::equal_to<mpl_::integral_c<boost::numeric::udt_builtin_mixture_enum, boost::numeric::builtin_to_builtin>, mpl_::integral_c<boost::numeric::udt_builtin_mixture_enum, boost::numeric::builtin_to_builtin>>' requested here  
   63 |           BOOST_MPL_AUX_STATIC_CAST(bool, BOOST_MPL_AUX_VALUE_WKND(T1)::value)  
      |                                                                    ^  
/ceph-inst/src/boost/boost/mpl/eval_if.hpp:37:22: note: in instantiation of template class 'boost::mpl::if_<boost::numeric::convdetail::equal_to<mpl_::integral_c<boost::numeric::udt_builtin_mixture_enum, boost::numeric::builtin_to_builtin>, mpl_::integral_c<boost::numeric::udt_builtin_mixture_enum, boost::numeric::builtin_to_builtin>>, boost::mpl::identity<boost::numeric::convdetail::get_subranged_BuiltIn2BuiltIn<long, unsigned int>>, boost::mpl::eval_if<boost::numeric::convdetail::equal_to<mpl_::integral_c<boost::numeric::udt_builtin_mixture_enum, boost::numeric::builtin_to_builtin>, mpl_::integral_c<boost::numeric::udt_builtin_mixture_enum, boost::numeric::builtin_to_udt>>, boost::mpl::identity<boost::mpl::identity<boost::numeric::convdetail::subranged_BuiltIn2Udt<long, unsigned int>>>, boost::mpl::if_<boost::numeric::convdetail::equal_to<mpl_::integral_c<boost::numeric::udt_builtin_mixture_enum, boost::numeric::builtin_to_builtin>, mpl_::integral_c<boost::numeric::udt_builtin_mixture_enum, boost::numeric::udt_to_builtin>>, boost::mpl::identity<boost::numeric::convdetail::subranged_Udt2BuiltIn<long, unsigned int>>, boost::mpl::identity<boost::numeric::convdetail::subranged_Udt2Udt<long, unsigned int>>>>>' requested here  
   37 |     typedef typename if_<C,F1,F2>::type f_;  
      |                      ^  
/ceph-inst/src/boost/boost/numeric/conversion/detail/meta.hpp:81:12: note: in instantiation of template class 'boost::mpl::eval_if<boost::numeric::convdetail::equal_to<mpl_::integral_c<boost::numeric::udt_builtin_mixture_enum, boost::numeric::builtin_to_builtin>, mpl_::integral_c<boost::numeric::udt_builtin_mixture_enum, boost::numeric::builtin_to_builtin>>, boost::mpl::identity<boost::numeric::convdetail::get_subranged_BuiltIn2BuiltIn<long, unsigned int>>, boost::mpl::eval_if<boost::numeric::convdetail::equal_to<mpl_::integral_c<boost::numeric::udt_builtin_mixture_enum, boost::numeric::builtin_to_builtin>, mpl_::integral_c<boost::numeric::udt_builtin_mixture_enum, boost::numeric::builtin_to_udt>>, boost::mpl::identity<boost::mpl::identity<boost::numeric::convdetail::subranged_BuiltIn2Udt<long, unsigned int>>>, boost::mpl::if_<boost::numeric::convdetail::equal_to<mpl_::integral_c<boost::numeric::udt_builtin_mixture_enum, boost::numeric::builtin_to_builtin>, mpl_::integral_c<boost::numeric::udt_builtin_mixture_enum, boost::numeric::udt_to_builtin>>, boost::mpl::identity<boost::numeric::convdetail::subranged_Udt2BuiltIn<long, unsigned int>>, boost::mpl::identity<boost::numeric::convdetail::subranged_Udt2Udt<long, unsigned int>>>>>' requested here  
   81 |       mpl::eval_if<is_case0,Case0TypeQ,choose_1_2_3Q>::type  
      |            ^  
/ceph-inst/src/boost/boost/numeric/conversion/detail/udt_builtin_mixture.hpp:41:7: note: in instantiation of template class 'boost::numeric::convdetail::ct_switch4<mpl_::integral_c<boost::numeric::udt_builtin_mixture_enum, boost::numeric::builtin_to_builtin>, mpl_::integral_c<boost::numeric::udt_builtin_mixture_enum, boost::numeric::builtin_to_builtin>, mpl_::integral_c<boost::numeric::udt_builtin_mixture_enum, boost::numeric::builtin_to_udt>, mpl_::integral_c<boost::numeric::udt_builtin_mixture_enum, boost::numeric::udt_to_builtin>, boost::numeric::convdetail::get_subranged_BuiltIn2BuiltIn<long, unsigned int>, boost::mpl::identity<boost::numeric::convdetail::subranged_BuiltIn2Udt<long, unsigned int>>, boost::mpl::identity<boost::numeric::convdetail::subranged_Udt2BuiltIn<long, unsigned int>>, boost::mpl::identity<boost::numeric::convdetail::subranged_Udt2Udt<long, unsigned int>>>' requested here  
   41 |       ct_switch4<UdtMixture  
      |       ^  
/ceph-inst/src/boost/boost/numeric/conversion/detail/is_subranged.hpp:205:9: note: (skipping 3 contexts in backtrace; use -ftemplate-backtrace-limit=0 to see all)  
  205 |         for_udt_builtin_mixture<udt_builtin_mixture, BuiltIn2BuiltInQ, BuiltIn2UdtQ, Udt2BuiltInQ, Udt2UdtQ>::type  
      |         ^  
/ceph-inst/src/boost/boost/numeric/conversion/conversion_traits.hpp:22:7: note: in instantiation of template class 'boost::numeric::convdetail::non_trivial_traits_impl<long, unsigned int>' requested here  
   22 |     : convdetail::get_conversion_traits<T,S>::type   
      |       ^  
/ceph-inst/src/boost/boost/numeric/conversion/detail/converter.hpp:584:22: note: in instantiation of template class 'boost::numeric::conversion_traits<long, unsigned int>' requested here  
  584 |     typedef typename Traits::trivial trivial ;  
      |                      ^  
/ceph-inst/src/boost/boost/numeric/conversion/converter.hpp:29:32: note: in instantiation of template class 'boost::numeric::convdetail::get_converter_impl<boost::numeric::conversion_traits<long, unsigned int>, boost::numeric::def_overflow_handler, boost::numeric::Trunc<unsigned int>, boost::numeric::raw_converter<boost::numeric::conversion_traits<long, unsigned int>>, boost::numeric::UseInternalRangeChecker>' requested here  
   29 | struct converter : convdetail::get_converter_impl<Traits,  
      |                                ^  
/ceph-inst/src/boost/boost/numeric/conversion/cast.hpp:53:16: note: in instantiation of template class 'boost::numeric::converter<long, unsigned int, boost::numeric::conversion_traits<long, unsigned int>, boost::numeric::def_overflow_handler, boost::numeric::Trunc<unsigned int>>' requested here  
   53 |         return converter::convert(arg);  
      |                ^  
/ceph-inst/src/boost/boost/date_time/posix_time/posix_time_duration.hpp:31:21: note: in instantiation of function template specialization 'boost::numeric_cast<long, unsigned int>' requested here  
   31 |       time_duration(numeric_cast<hour_type>(h), 0, 0)  
      |                     ^  
/ceph-inst/src/boost/boost/mpl/aux_/integral_wrapper.hpp:73:31: note: integer value -1 is outside the valid range of values [0, 3] for the enumeration type 'udt_builtin_mixture_enum'  
   73 |     typedef AUX_WRAPPER_INST( BOOST_MPL_AUX_STATIC_CAST(AUX_WRAPPER_VALUE_TYPE, (value - 1)) ) prior;  
      |                               ^  
/ceph-inst/src/boost/boost/mpl/aux_/static_cast.hpp:24:47: note: expanded from macro 'BOOST_MPL_AUX_STATIC_CAST'  
   24 | #   define BOOST_MPL_AUX_STATIC_CAST(T, expr) static_cast<T>(expr)  
      |                                               ^  
In file included from /ceph-inst/src/rgw/rgw_s3select.cc:4:  
In file included from /ceph-inst/src/rgw/rgw_s3select_private.h:11:  
In file included from /ceph-inst/src/common/ceph_crypto.h:10:  
In file included from /ceph-inst/src/include/types.h:21:  
In file included from /ceph-inst/src/include/uuid.h:9:  
In file included from /ceph-inst/src/include/encoding.h:41:  
In file included from /ceph-inst/src/include/denc.h:51:  
In file included from /ceph-inst/src/common/error_code.h:22:  
In file included from /ceph-inst/src/boost/boost/asio.hpp:25:  
In file included from /ceph-inst/src/boost/boost/asio/basic_deadline_timer.hpp:25:  
In file included from /ceph-inst/src/boost/boost/asio/detail/deadline_timer_service.hpp:29:  
In file included from /ceph-inst/src/boost/boost/asio/detail/timer_queue_ptime.hpp:22:  
In file included from /ceph-inst/src/boost/boost/asio/time_traits.hpp:23:  
In file included from /ceph-inst/src/boost/boost/date_time/posix_time/posix_time_types.hpp:16:  
In file included from /ceph-inst/src/boost/boost/date_time/posix_time/posix_time_duration.hpp:15:  
In file included from /ceph-inst/src/boost/boost/numeric/conversion/cast.hpp:33:  
In file included from /ceph-inst/src/boost/boost/numeric/conversion/converter.hpp:13:  
In file included from /ceph-inst/src/boost/boost/numeric/conversion/conversion_traits.hpp:13:  
In file included from /ceph-inst/src/boost/boost/numeric/conversion/detail/conversion_traits.hpp:18:  
In file included from /ceph-inst/src/boost/boost/numeric/conversion/detail/int_float_mixture.hpp:19:  
In file included from /ceph-inst/src/boost/boost/mpl/integral_c.hpp:32:  
/ceph-inst/src/boost/boost/mpl/aux_/integral_wrapper.hpp:73:31: error: non-type template argument is not a constant expression  
   73 |     typedef AUX_WRAPPER_INST( BOOST_MPL_AUX_STATIC_CAST(AUX_WRAPPER_VALUE_TYPE, (value - 1)) ) prior;  
      |             ~~~~~~~~~~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
/ceph-inst/src/boost/boost/mpl/aux_/static_cast.hpp:24:47: note: expanded from macro 'BOOST_MPL_AUX_STATIC_CAST'  
   24 | #   define BOOST_MPL_AUX_STATIC_CAST(T, expr) static_cast<T>(expr)  
      |                                               ^  
/ceph-inst/src/boost/boost/mpl/integral_c.hpp:31:54: note: expanded from macro 'AUX_WRAPPER_INST'  
   31 | #define AUX_WRAPPER_INST(value) AUX_WRAPPER_NAME< T, value >  
      |                                                      ^~~~~  
/ceph-inst/src/boost/boost/numeric/conversion/detail/meta.hpp:30:46: note: in instantiation of template class 'mpl_::integral_c<boost::numeric::int_float_mixture_enum, boost::numeric::integral_to_integral>' requested here  
   30 |        enum { x = ( BOOST_MPL_AUX_VALUE_WKND(T1)::value == BOOST_MPL_AUX_VALUE_WKND(T2)::value ) };  
      |                                              ^  
/ceph-inst/src/boost/boost/mpl/if.hpp:63:68: note: in instantiation of template class 'boost::numeric::convdetail::equal_to<mpl_::integral_c<boost::numeric::int_float_mixture_enum, boost::numeric::integral_to_integral>, mpl_::integral_c<boost::numeric::int_float_mixture_enum, boost::numeric::integral_to_integral>>' requested here  
   63 |           BOOST_MPL_AUX_STATIC_CAST(bool, BOOST_MPL_AUX_VALUE_WKND(T1)::value)  
      |                                                                    ^  
/ceph-inst/src/boost/boost/mpl/eval_if.hpp:37:22: note: in instantiation of template class 'boost::mpl::if_<boost::numeric::convdetail::equal_to<mpl_::integral_c<boost::numeric::int_float_mixture_enum, boost::numeric::integral_to_integral>, mpl_::integral_c<boost::numeric::int_float_mixture_enum, boost::numeric::integral_to_integral>>, boost::mpl::identity<boost::mpl::identity<boost::numeric::convdetail::non_rounding_converter<boost::numeric::conversion_traits<long, unsigned int>, boost::numeric::convdetail::dummy_range_checker<boost::numeric::conversion_traits<long, unsigned int>>, boost::numeric::raw_converter<boost::numeric::conversion_traits<long, unsigned int>>>>>, boost::mpl::eval_if<boost::numeric::convdetail::equal_to<mpl_::integral_c<boost::numeric::int_float_mixture_enum, boost::numeric::integral_to_integral>, mpl_::integral_c<boost::numeric::int_float_mixture_enum, boost::numeric::integral_to_float>>, boost::mpl::identity<boost::mpl::identity<boost::numeric::convdetail::non_rounding_converter<boost::numeric::conversion_traits<long, unsigned int>, boost::numeric::convdetail::dummy_range_checker<boost::numeric::conversion_traits<long, unsigned int>>, boost::numeric::raw_converter<boost::numeric::conversion_traits<long, unsigned int>>>>>, boost::mpl::if_<boost::numeric::convdetail::equal_to<mpl_::integral_c<boost::numeric::int_float_mixture_enum, boost::numeric::integral_to_integral>, mpl_::integral_c<boost::numeric::int_float_mixture_enum, boost::numeric::float_to_integral>>, boost::mpl::identity<boost::numeric::convdetail::rounding_converter<boost::numeric::conversion_traits<long, unsigned int>, boost::numeric::convdetail::dummy_range_checker<boost::numeric::conversion_traits<long, unsigned int>>, boost::numeric::raw_converter<boost::numeric::conversion_traits<long, unsigned int>>, boost::numeric::Trunc<unsigned int>>>, boost::mpl::identity<boost::numeric::convdetail::non_rounding_converter<boost::numeric::conversion_traits<long, unsigned int>, boost::numeric::convdetail::dummy_range_checker<boost::numeric::conversion_traits<long, unsigned int>>, boost::numeric::raw_converter<boost::numeric::conversion_traits<long, unsigned int>>>>>>>' requested here  
   37 |     typedef typename if_<C,F1,F2>::type f_;  
      |                      ^  
/ceph-inst/src/boost/boost/numeric/conversion/detail/meta.hpp:81:12: note: in instantiation of template class 'boost::mpl::eval_if<boost::numeric::convdetail::equal_to<mpl_::integral_c<boost::numeric::int_float_mixture_enum, boost::numeric::integral_to_integral>, mpl_::integral_c<boost::numeric::int_float_mixture_enum, boost::numeric::integral_to_integral>>, boost::mpl::identity<boost::mpl::identity<boost::numeric::convdetail::non_rounding_converter<boost::numeric::conversion_traits<long, unsigned int>, boost::numeric::convdetail::dummy_range_checker<boost::numeric::conversion_traits<long, unsigned int>>, boost::numeric::raw_converter<boost::numeric::conversion_traits<long, unsigned int>>>>>, boost::mpl::eval_if<boost::numeric::convdetail::equal_to<mpl_::integral_c<boost::numeric::int_float_mixture_enum, boost::numeric::integral_to_integral>, mpl_::integral_c<boost::numeric::int_float_mixture_enum, boost::numeric::integral_to_float>>, boost::mpl::identity<boost::mpl::identity<boost::numeric::convdetail::non_rounding_converter<boost::numeric::conversion_traits<long, unsigned int>, boost::numeric::convdetail::dummy_range_checker<boost::numeric::conversion_traits<long, unsigned int>>, boost::numeric::raw_converter<boost::numeric::conversion_traits<long, unsigned int>>>>>, boost::mpl::if_<boost::numeric::convdetail::equal_to<mpl_::integral_c<boost::numeric::int_float_mixture_enum, boost::numeric::integral_to_integral>, mpl_::integral_c<boost::numeric::int_float_mixture_enum, boost::numeric::float_to_integral>>, boost::mpl::identity<boost::numeric::convdetail::rounding_converter<boost::numeric::conversion_traits<long, unsigned int>, boost::numeric::convdetail::dummy_range_checker<boost::numeric::conversion_traits<long, unsigned int>>, boost::numeric::raw_converter<boost::numeric::conversion_traits<long, unsigned int>>, boost::numeric::Trunc<unsigned int>>>, boost::mpl::identity<boost::numeric::convdetail::non_rounding_converter<boost::numeric::conversion_traits<long, unsigned int>, boost::numeric::convdetail::dummy_range_checker<boost::numeric::conversion_traits<long, unsigned int>>, boost::numeric::raw_converter<boost::numeric::conversion_traits<long, unsigned int>>>>>>>' requested here  
   81 |       mpl::eval_if<is_case0,Case0TypeQ,choose_1_2_3Q>::type  
      |            ^  
/ceph-inst/src/boost/boost/numeric/conversion/detail/int_float_mixture.hpp:59:7: note: in instantiation of template class 'boost::numeric::convdetail::ct_switch4<mpl_::integral_c<boost::numeric::int_float_mixture_enum, boost::numeric::integral_to_integral>, mpl_::integral_c<boost::numeric::int_float_mixture_enum, boost::numeric::integral_to_integral>, mpl_::integral_c<boost::numeric::int_float_mixture_enum, boost::numeric::integral_to_float>, mpl_::integral_c<boost::numeric::int_float_mixture_enum, boost::numeric::float_to_integral>, boost::mpl::identity<boost::numeric::convdetail::non_rounding_converter<boost::numeric::conversion_traits<long, unsigned int>, boost::numeric::convdetail::dummy_range_checker<boost::numeric::conversion_traits<long, unsigned int>>, boost::numeric::raw_converter<boost::numeric::conversion_traits<long, unsigned int>>>>, boost::mpl::identity<boost::numeric::convdetail::non_rounding_converter<boost::numeric::conversion_traits<long, unsigned int>, boost::numeric::convdetail::dummy_range_checker<boost::numeric::conversion_traits<long, unsigned int>>, boost::numeric::raw_converter<boost::numeric::conversion_traits<long, unsigned int>>>>, boost::mpl::identity<boost::numeric::convdetail::rounding_converter<boost::numeric::conversion_traits<long, unsigned int>, boost::numeric::convdetail::dummy_range_checker<boost::numeric::conversion_traits<long, unsigned int>>, boost::numeric::raw_converter<boost::numeric::conversion_traits<long, unsigned int>>, boost::numeric::Trunc<unsigned int>>>, boost::mpl::identity<boost::numeric::convdetail::non_rounding_converter<boost::numeric::conversion_traits<long, unsigned int>, boost::numeric::convdetail::dummy_range_checker<boost::numeric::conversion_traits<long, unsigned int>>, boost::numeric::raw_converter<boost::numeric::conversion_traits<long, unsigned int>>>>>' requested here  
   59 |       ct_switch4<IntFloatMixture  
      |       ^  
/ceph-inst/src/boost/boost/numeric/conversion/detail/converter.hpp:550:7: note: (skipping 1 context in backtrace; use -ftemplate-backtrace-limit=0 to see all)  
  550 |       for_int_float_mixture<int_float_mixture, NonRoundingQ, NonRoundingQ, RoundingQ, NonRoundingQ>::type  
      |       ^  
/ceph-inst/src/boost/boost/mpl/eval_if.hpp:38:22: note: in instantiation of template class 'boost::numeric::convdetail::get_non_trivial_converter<boost::numeric::conversion_traits<long, unsigned int>, boost::numeric::def_overflow_handler, boost::numeric::Trunc<unsigned int>, boost::numeric::raw_converter<boost::numeric::conversion_traits<long, unsigned int>>, boost::numeric::UseInternalRangeChecker>' requested here  
   38 |     typedef typename f_::type type;  
      |                      ^  
/ceph-inst/src/boost/boost/numeric/conversion/detail/converter.hpp:586:27: note: in instantiation of template class 'boost::mpl::eval_if<mpl_::bool_<false>, boost::mpl::identity<boost::numeric::convdetail::trivial_converter_impl<boost::numeric::conversion_traits<long, unsigned int>>>, boost::numeric::convdetail::get_non_trivial_converter<boost::numeric::conversion_traits<long, unsigned int>, boost::numeric::def_overflow_handler, boost::numeric::Trunc<unsigned int>, boost::numeric::raw_converter<boost::numeric::conversion_traits<long, unsigned int>>, boost::numeric::UseInternalRangeChecker>>' requested here  
  586 |     typedef typename mpl::eval_if<trivial,TrivialQ,NonTrivialQ>::type type ;  
      |                           ^  
/ceph-inst/src/boost/boost/numeric/conversion/converter.hpp:29:32: note: in instantiation of template class 'boost::numeric::convdetail::get_converter_impl<boost::numeric::conversion_traits<long, unsigned int>, boost::numeric::def_overflow_handler, boost::numeric::Trunc<unsigned int>, boost::numeric::raw_converter<boost::numeric::conversion_traits<long, unsigned int>>, boost::numeric::UseInternalRangeChecker>' requested here  
   29 | struct converter : convdetail::get_converter_impl<Traits,  
      |                                ^  
/ceph-inst/src/boost/boost/numeric/conversion/cast.hpp:53:16: note: in instantiation of template class 'boost::numeric::converter<long, unsigned int, boost::numeric::conversion_traits<long, unsigned int>, boost::numeric::def_overflow_handler, boost::numeric::Trunc<unsigned int>>' requested here  
   53 |         return converter::convert(arg);  
      |                ^  
/ceph-inst/src/boost/boost/date_time/posix_time/posix_time_duration.hpp:31:21: note: in instantiation of function template specialization 'boost::numeric_cast<long, unsigned int>' requested here  
   31 |       time_duration(numeric_cast<hour_type>(h), 0, 0)  
      |                     ^  
/ceph-inst/src/boost/boost/mpl/aux_/integral_wrapper.hpp:73:31: note: integer value -1 is outside the valid range of values [0, 3] for the enumeration type 'int_float_mixture_enum'  
   73 |     typedef AUX_WRAPPER_INST( BOOST_MPL_AUX_STATIC_CAST(AUX_WRAPPER_VALUE_TYPE, (value - 1)) ) prior;  
      |                               ^  
/ceph-inst/src/boost/boost/mpl/aux_/static_cast.hpp:24:47: note: expanded from macro 'BOOST_MPL_AUX_STATIC_CAST'  
   24 | #   define BOOST_MPL_AUX_STATIC_CAST(T, expr) static_cast<T>(expr)  
      |                                               ^  
```

---

## Comment 1

> Username: alexvorndran  
> Created at: 2026-02-14 21:41:35 UTC  
> Url: https://github.com/boostorg/mpl/issues/88#issuecomment-3902568419  

I have recently also encountered this issue when I tried to use clang++ 21 (and also clang 18) in the build for a legacy system running Ubuntu 18.04 ESM with boost 1.65.1 and found this open issue. I found this to be related to #69. As @pdimov noted in https://github.com/boostorg/mpl/issues/69#issuecomment-2174666767, this should be fixed in boost 1.86 and newer.  
  
As per another comment in this issue (https://github.com/boostorg/mpl/issues/69#issuecomment-2301411819) there are two related changes that can be backported so solve this issue:  
  
- https://github.com/boostorg/mpl/commit/fb6b861834e29a93ba71a2e2501a42ecfd3c5655  
- https://github.com/boostorg/numeric_conversion/commit/50a1eae942effb0a9b90724323ef8f2a67e7984a
