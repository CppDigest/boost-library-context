# #41 - error cross-compiling for mingw-w64 on gentoo. [Closed]

> Username: hanetzer  
> Created at: 2017-09-21 06:28:15 UTC  
> Updated at: 2017-09-21 22:31:29 UTC  
> Closed at: 2017-09-21 22:31:28 UTC  
> Url: https://github.com/boostorg/log/issues/41  

As stated in the title.  
  
compile command line:  
`b2 --user-config=/tmp/portage/dev-libs/boost-1.65.0-r1/work/boost_1_65_0-abi_x86_32.x86/user-config.jam gentoorelease -j16 -q -d+2 -sICU_PATH=/usr --without-mpi --without-context --without-coroutine --without-fiber pch=off --boost-build=/usr/share/boost-build --prefix=/tmp/portage/dev-libs/boost-1.65.0-r1/image/usr --layout=system threading=multi link=shared -sNO_BZIP2=1 target-os=windows --without-python`  
  
user-config.jam:  
`using gcc : 6.4 : i686-w64-mingw32-g++ : <cflags>"-O2 -pipe -fomit-frame-pointer" <cxxflags>"-O2 -pipe -fomit-frame-pointer -std=c++14" <linkflags>"-Wl,-O1 -Wl,--as-needed" ;`  
  
build.log:  
https://bpaste.net/show/0f4ad2771c83  
  
if you need any other info, please let me know.

---

## Comment 1

> Username: hanetzer  
> Created at: 2017-09-21 06:48:39 UTC  
> Url: https://github.com/boostorg/log/issues/41#issuecomment-331067664  

```  
gcc.link.dll bin.v2/libs/log/build/gcc-mingw-6.4/gentoorelease/build-no/pch-off/target-os-windows/threading-multi/libboost_log.dll.a  
  
    "i686-w64-mingw32-g++"   "-Wl,--out-implib,bin.v2/libs/log/build/gcc-mingw-6.4/gentoorelease/build-no/pch-off/target-os-windows/threading-multi/libboost_log.dll.a" -o "bin.v2/libs/log/build/gcc-mingw-6.4/gentoorelease/build-no/pch-off/target-os-windows/threading-multi/libboost_log.dll" -Wl,-h -Wl,libboost_log.dll -shared -Wl,--start-group "bin.v2/libs/log/build/gcc-mingw-6.4/gentoorelease/build-no/pch-off/target-os-windows/threading-multi/attribute_name.o" "bin.v2/libs/log/build/gcc-mingw-6.4/gentoorelease/build-no/pch-off/target-os-windows/threading-multi/attribute_set.o" "bin.v2/libs/log/build/gcc-mingw-6.4/gentoorelease/build-no/pch-off/target-os-windows/threading-multi/attribute_value_set.o" "bin.v2/libs/log/build/gcc-mingw-6.4/gentoorelease/build-no/pch-off/target-os-windows/threading-multi/code_conversion.o" "bin.v2/libs/log/build/gcc-mingw-6.4/gentoorelease/build-no/pch-off/target-os-windows/threading-multi/core.o" "bin.v2/libs/log/build/gcc-mingw-6.4/gentoorelease/build-no/pch-off/target-os-windows/threading-multi/record_ostream.o" "bin.v2/libs/log/build/gcc-mingw-6.4/gentoorelease/build-no/pch-off/target-os-windows/threading-multi/severity_level.o" "bin.v2/libs/log/build/gcc-mingw-6.4/gentoorelease/build-no/pch-off/target-os-windows/threading-multi/global_logger_storage.o" "bin.v2/libs/log/build/gcc-mingw-6.4/gentoorelease/build-no/pch-off/target-os-windows/threading-multi/named_scope.o" "bin.v2/libs/log/build/gcc-mingw-6.4/gentoorelease/build-no/pch-off/target-os-windows/threading-multi/process_name.o" "bin.v2/libs/log/build/gcc-mingw-6.4/gentoorelease/build-no/pch-off/target-os-windows/threading-multi/process_id.o" "bin.v2/libs/log/build/gcc-mingw-6.4/gentoorelease/build-no/pch-off/target-os-windows/threading-multi/thread_id.o" "bin.v2/libs/log/build/gcc-mingw-6.4/gentoorelease/build-no/pch-off/target-os-windows/threading-multi/timer.o" "bin.v2/libs/log/build/gcc-mingw-6.4/gentoorelease/build-no/pch-off/target-os-windows/threading-multi/exceptions.o" "bin.v2/libs/log/build/gcc-mingw-6.4/gentoorelease/build-no/pch-off/target-os-windows/threading-multi/default_attribute_names.o" "bin.v2/libs/log/build/gcc-mingw-6.4/gentoorelease/build-no/pch-off/target-os-windows/threading-multi/default_sink.o" "bin.v2/libs/log/build/gcc-mingw-6.4/gentoorelease/build-no/pch-off/target-os-windows/threading-multi/text_ostream_backend.o" "bin.v2/libs/log/build/gcc-mingw-6.4/gentoorelease/build-no/pch-off/target-os-windows/threading-multi/text_file_backend.o" "bin.v2/libs/log/build/gcc-mingw-6.4/gentoorelease/build-no/pch-off/target-os-windows/threading-multi/text_multifile_backend.o" "bin.v2/libs/log/build/gcc-mingw-6.4/gentoorelease/build-no/pch-off/target-os-windows/threading-multi/thread_specific.o" "bin.v2/libs/log/build/gcc-mingw-6.4/gentoorelease/build-no/pch-off/target-os-windows/threading-multi/once_block.o" "bin.v2/libs/log/build/gcc-mingw-6.4/gentoorelease/build-no/pch-off/target-os-windows/threading-multi/timestamp.o" "bin.v2/libs/log/build/gcc-mingw-6.4/gentoorelease/build-no/pch-off/target-os-windows/threading-multi/threadsafe_queue.o" "bin.v2/libs/log/build/gcc-mingw-6.4/gentoorelease/build-no/pch-off/target-os-windows/threading-multi/event.o" "bin.v2/libs/log/build/gcc-mingw-6.4/gentoorelease/build-no/pch-off/target-os-windows/threading-multi/trivial.o" "bin.v2/libs/log/build/gcc-mingw-6.4/gentoorelease/build-no/pch-off/target-os-windows/threading-multi/spirit_encoding.o" "bin.v2/libs/log/build/gcc-mingw-6.4/gentoorelease/build-no/pch-off/target-os-windows/threading-multi/format_parser.o" "bin.v2/libs/log/build/gcc-mingw-6.4/gentoorelease/build-no/pch-off/target-os-windows/threading-multi/date_time_format_parser.o" "bin.v2/libs/log/build/gcc-mingw-6.4/gentoorelease/build-no/pch-off/target-os-windows/threading-multi/named_scope_format_parser.o" "bin.v2/libs/log/build/gcc-mingw-6.4/gentoorelease/build-no/pch-off/target-os-windows/threading-multi/unhandled_exception_count.o" "bin.v2/libs/log/build/gcc-mingw-6.4/gentoorelease/build-no/pch-off/target-os-windows/threading-multi/permissions.o" "bin.v2/libs/log/build/gcc-mingw-6.4/gentoorelease/build-no/pch-off/target-os-windows/threading-multi/dump.o" "bin.v2/libs/log/build/gcc-mingw-6.4/gentoorelease/build-no/pch-off/target-os-windows/threading-multi/dump_avx2.o" "bin.v2/libs/log/build/gcc-mingw-6.4/gentoorelease/build-no/pch-off/target-os-windows/threading-multi/dump_ssse3.o" "bin.v2/libs/log/build/gcc-mingw-6.4/gentoorelease/build-no/pch-off/target-os-windows/threading-multi/syslog_backend.o" "bin.v2/libs/log/build/gcc-mingw-6.4/gentoorelease/build-no/pch-off/target-os-windows/threading-multi/windows/debug_output_backend.o" "bin.v2/libs/log/build/gcc-mingw-6.4/gentoorelease/build-no/pch-off/target-os-windows/threading-multi/windows/ipc_reliable_message_queue.o" "bin.v2/libs/log/build/gcc-mingw-6.4/gentoorelease/build-no/pch-off/target-os-windows/threading-multi/windows/ipc_sync_wrappers.o" "bin.v2/libs/log/build/gcc-mingw-6.4/gentoorelease/build-no/pch-off/target-os-windows/threading-multi/windows/light_rw_mutex.o" "bin.v2/libs/log/build/gcc-mingw-6.4/gentoorelease/build-no/pch-off/target-os-windows/threading-multi/windows/mapped_shared_memory.o" "bin.v2/libs/log/build/gcc-mingw-6.4/gentoorelease/build-no/pch-off/target-os-windows/threading-multi/windows/object_name.o" "bin.v2/libs/atomic/build/gcc-mingw-6.4/gentoorelease/pch-off/target-os-windows/threading-multi/libboost_atomic.dll.a" "bin.v2/libs/date_time/build/gcc-mingw-6.4/gentoorelease/pch-off/target-os-windows/threading-multi/libboost_date_time.dll.a" "bin.v2/libs/filesystem/build/gcc-mingw-6.4/gentoorelease/pch-off/target-os-windows/threading-multi/libboost_filesystem.dll.a" "bin.v2/libs/system/build/gcc-mingw-6.4/gentoorelease/pch-off/target-os-windows/threading-multi/libboost_system.dll.a" "bin.v2/libs/regex/build/gcc-mingw-6.4/gentoorelease/pch-off/target-os-windows/threading-multi/libboost_regex.dll.a"  -Wl,-Bstatic  -Wl,-Bdynamic -ladvapi32 -lsecur32 -lws2_32 -lmswsock -Wl,--end-group -Wl,-O1 -Wl,--as-needed -march=i686 -mthreads -m32 -Wl,--enable-auto-import  
  
bin.v2/libs/log/build/gcc-mingw-6.4/gentoorelease/build-no/pch-off/target-os-windows/threading-multi/core.o:core.cpp:(.text+0x26b): undefined reference to `boost::detail::set_tss_data(void const*, boost::shared_ptr<boost::detail::tss_cleanup_function>, void*, bool)'  
bin.v2/libs/log/build/gcc-mingw-6.4/gentoorelease/build-no/pch-off/target-os-windows/threading-multi/core.o:core.cpp:(.text+0x15f7): undefined reference to `boost::detail::get_tss_data(void const*)'  
bin.v2/libs/log/build/gcc-mingw-6.4/gentoorelease/build-no/pch-off/target-os-windows/threading-multi/core.o:core.cpp:(.text+0x187d): undefined reference to `boost::detail::get_tss_data(void const*)'  
bin.v2/libs/log/build/gcc-mingw-6.4/gentoorelease/build-no/pch-off/target-os-windows/threading-multi/core.o:core.cpp:(.text+0x1cae): undefined reference to `boost::detail::get_tss_data(void const*)'  
bin.v2/libs/log/build/gcc-mingw-6.4/gentoorelease/build-no/pch-off/target-os-windows/threading-multi/core.o:core.cpp:(.text+0x1ceb): undefined reference to `boost::detail::get_tss_data(void const*)'  
bin.v2/libs/log/build/gcc-mingw-6.4/gentoorelease/build-no/pch-off/target-os-windows/threading-multi/core.o:core.cpp:(.text+0x1d14): undefined reference to `boost::detail::get_tss_data(void const*)'  
bin.v2/libs/log/build/gcc-mingw-6.4/gentoorelease/build-no/pch-off/target-os-windows/threading-multi/core.o:core.cpp:(.text+0x1d50): more undefined references to `boost::detail::get_tss_data(void const*)' follow  
bin.v2/libs/log/build/gcc-mingw-6.4/gentoorelease/build-no/pch-off/target-os-windows/threading-multi/core.o:core.cpp:(.text+0x36c7): undefined reference to `boost::detail::set_tss_data(void const*, boost::shared_ptr<boost::detail::tss_cleanup_function>, void*, bool)'  
bin.v2/libs/log/build/gcc-mingw-6.4/gentoorelease/build-no/pch-off/target-os-windows/threading-multi/core.o:core.cpp:(.text$_ZN5boost19thread_specific_ptrINS_3log9v2_mt_nt54core14implementation11thread_dataEED1Ev[__ZN5boost19thread_specific_ptrINS_3log9v2_mt_nt54core14implementation11thread_dataEED1Ev]+0x5c): undefined reference to `boost::detail::set_tss_data(void const*, boost::shared_ptr<boost::detail::tss_cleanup_function>, void*, bool)'  
bin.v2/libs/log/build/gcc-mingw-6.4/gentoorelease/build-no/pch-off/target-os-windows/threading-multi/core.o:core.cpp:(.text$_ZN5boost3log9v2_mt_nt54core14implementation16init_thread_dataEv[__ZN5boost3log9v2_mt_nt54core14implementation16init_thread_dataEv]+0x72): undefined reference to `boost::detail::get_tss_data(void const*)'  
bin.v2/libs/log/build/gcc-mingw-6.4/gentoorelease/build-no/pch-off/target-os-windows/threading-multi/core.o:core.cpp:(.text$_ZN5boost3log9v2_mt_nt54core14implementation16init_thread_dataEv[__ZN5boost3log9v2_mt_nt54core14implementation16init_thread_dataEv]+0x447): undefined reference to `boost::detail::get_tss_data(void const*)'  
bin.v2/libs/log/build/gcc-mingw-6.4/gentoorelease/build-no/pch-off/target-os-windows/threading-multi/core.o:core.cpp:(.text$_ZN5boost3log9v2_mt_nt54core14implementation16init_thread_dataEv[__ZN5boost3log9v2_mt_nt54core14implementation16init_thread_dataEv]+0x4a0): undefined reference to `boost::detail::set_tss_data(void const*, boost::shared_ptr<boost::detail::tss_cleanup_function>, void*, bool)'  
bin.v2/libs/log/build/gcc-mingw-6.4/gentoorelease/build-no/pch-off/target-os-windows/threading-multi/record_ostream.o:record_ostream.cpp:(.text+0x2d4): undefined reference to `boost::detail::set_tss_data(void const*, boost::shared_ptr<boost::detail::tss_cleanup_function>, void*, bool)'  
bin.v2/libs/log/build/gcc-mingw-6.4/gentoorelease/build-no/pch-off/target-os-windows/threading-multi/record_ostream.o:record_ostream.cpp:(.text+0x3d4): undefined reference to `boost::detail::set_tss_data(void const*, boost::shared_ptr<boost::detail::tss_cleanup_function>, void*, bool)'  
bin.v2/libs/log/build/gcc-mingw-6.4/gentoorelease/build-no/pch-off/target-os-windows/threading-multi/record_ostream.o:record_ostream.cpp:(.text$_ZN5boost3log9v2_mt_nt53aux15stream_providerIcE16release_compoundEPNS4_15stream_compoundE[__ZN5boost3log9v2_mt_nt53aux15stream_providerIcE16release_compoundEPNS4_15stream_compoundE]+0x81): undefined reference to `boost::detail::get_tss_data(void const*)'  
bin.v2/libs/log/build/gcc-mingw-6.4/gentoorelease/build-no/pch-off/target-os-windows/threading-multi/record_ostream.o:record_ostream.cpp:(.text$_ZN5boost3log9v2_mt_nt53aux15stream_providerIcE16release_compoundEPNS4_15stream_compoundE[__ZN5boost3log9v2_mt_nt53aux15stream_providerIcE16release_compoundEPNS4_15stream_compoundE]+0x2cb): undefined reference to `boost::detail::get_tss_data(void const*)'  
bin.v2/libs/log/build/gcc-mingw-6.4/gentoorelease/build-no/pch-off/target-os-windows/threading-multi/record_ostream.o:record_ostream.cpp:(.text$_ZN5boost3log9v2_mt_nt53aux15stream_providerIcE16release_compoundEPNS4_15stream_compoundE[__ZN5boost3log9v2_mt_nt53aux15stream_providerIcE16release_compoundEPNS4_15stream_compoundE]+0x316): undefined reference to `boost::detail::set_tss_data(void const*, boost::shared_ptr<boost::detail::tss_cleanup_function>, void*, bool)'  
bin.v2/libs/log/build/gcc-mingw-6.4/gentoorelease/build-no/pch-off/target-os-windows/threading-multi/record_ostream.o:record_ostream.cpp:(.text$_ZN5boost3log9v2_mt_nt53aux15stream_providerIwE16release_compoundEPNS4_15stream_compoundE[__ZN5boost3log9v2_mt_nt53aux15stream_providerIwE16release_compoundEPNS4_15stream_compoundE]+0x7c): undefined reference to `boost::detail::get_tss_data(void const*)'  
bin.v2/libs/log/build/gcc-mingw-6.4/gentoorelease/build-no/pch-off/target-os-windows/threading-multi/record_ostream.o:record_ostream.cpp:(.text$_ZN5boost3log9v2_mt_nt53aux15stream_providerIwE16release_compoundEPNS4_15stream_compoundE[__ZN5boost3log9v2_mt_nt53aux15stream_providerIwE16release_compoundEPNS4_15stream_compoundE]+0x2cb): undefined reference to `boost::detail::get_tss_data(void const*)'  
bin.v2/libs/log/build/gcc-mingw-6.4/gentoorelease/build-no/pch-off/target-os-windows/threading-multi/record_ostream.o:record_ostream.cpp:(.text$_ZN5boost3log9v2_mt_nt53aux15stream_providerIwE16release_compoundEPNS4_15stream_compoundE[__ZN5boost3log9v2_mt_nt53aux15stream_providerIwE16release_compoundEPNS4_15stream_compoundE]+0x316): undefined reference to `boost::detail::set_tss_data(void const*, boost::shared_ptr<boost::detail::tss_cleanup_function>, void*, bool)'  
bin.v2/libs/log/build/gcc-mingw-6.4/gentoorelease/build-no/pch-off/target-os-windows/threading-multi/record_ostream.o:record_ostream.cpp:(.text$_ZN5boost3log9v2_mt_nt53aux15stream_providerIcE17allocate_compoundERNS1_6recordE[__ZN5boost3log9v2_mt_nt53aux15stream_providerIcE17allocate_compoundERNS1_6recordE]+0x84): undefined reference to `boost::detail::get_tss_data(void const*)'  
bin.v2/libs/log/build/gcc-mingw-6.4/gentoorelease/build-no/pch-off/target-os-windows/threading-multi/record_ostream.o:record_ostream.cpp:(.text$_ZN5boost3log9v2_mt_nt53aux15stream_providerIcE17allocate_compoundERNS1_6recordE[__ZN5boost3log9v2_mt_nt53aux15stream_providerIcE17allocate_compoundERNS1_6recordE]+0x4ab): undefined reference to `boost::detail::get_tss_data(void const*)'  
bin.v2/libs/log/build/gcc-mingw-6.4/gentoorelease/build-no/pch-off/target-os-windows/threading-multi/record_ostream.o:record_ostream.cpp:(.text$_ZN5boost3log9v2_mt_nt53aux15stream_providerIcE17allocate_compoundERNS1_6recordE[__ZN5boost3log9v2_mt_nt53aux15stream_providerIcE17allocate_compoundERNS1_6recordE]+0x4f6): undefined reference to `boost::detail::set_tss_data(void const*, boost::shared_ptr<boost::detail::tss_cleanup_function>, void*, bool)'  
bin.v2/libs/log/build/gcc-mingw-6.4/gentoorelease/build-no/pch-off/target-os-windows/threading-multi/record_ostream.o:record_ostream.cpp:(.text$_ZN5boost3log9v2_mt_nt53aux15stream_providerIwE17allocate_compoundERNS1_6recordE[__ZN5boost3log9v2_mt_nt53aux15stream_providerIwE17allocate_compoundERNS1_6recordE]+0x7f): undefined reference to `boost::detail::get_tss_data(void const*)'  
bin.v2/libs/log/build/gcc-mingw-6.4/gentoorelease/build-no/pch-off/target-os-windows/threading-multi/record_ostream.o:record_ostream.cpp:(.text$_ZN5boost3log9v2_mt_nt53aux15stream_providerIwE17allocate_compoundERNS1_6recordE[__ZN5boost3log9v2_mt_nt53aux15stream_providerIwE17allocate_compoundERNS1_6recordE]+0x4ab): undefined reference to `boost::detail::get_tss_data(void const*)'  
bin.v2/libs/log/build/gcc-mingw-6.4/gentoorelease/build-no/pch-off/target-os-windows/threading-multi/record_ostream.o:record_ostream.cpp:(.text$_ZN5boost3log9v2_mt_nt53aux15stream_providerIwE17allocate_compoundERNS1_6recordE[__ZN5boost3log9v2_mt_nt53aux15stream_providerIwE17allocate_compoundERNS1_6recordE]+0x4f6): undefined reference to `boost::detail::set_tss_data(void const*, boost::shared_ptr<boost::detail::tss_cleanup_function>, void*, bool)'  
bin.v2/libs/log/build/gcc-mingw-6.4/gentoorelease/build-no/pch-off/target-os-windows/threading-multi/severity_level.o:severity_level.cpp:(.text+0x191): undefined reference to `boost::detail::add_thread_exit_function(boost::detail::thread_exit_function_base*)'  
bin.v2/libs/log/build/gcc-mingw-6.4/gentoorelease/build-no/pch-off/target-os-windows/threading-multi/named_scope.o:named_scope.cpp:(.text+0x613): undefined reference to `boost::detail::get_tss_data(void const*)'  
bin.v2/libs/log/build/gcc-mingw-6.4/gentoorelease/build-no/pch-off/target-os-windows/threading-multi/named_scope.o:named_scope.cpp:(.text+0x685): undefined reference to `boost::detail::get_tss_data(void const*)'  
bin.v2/libs/log/build/gcc-mingw-6.4/gentoorelease/build-no/pch-off/target-os-windows/threading-multi/named_scope.o:named_scope.cpp:(.text+0x6d7): undefined reference to `boost::detail::set_tss_data(void const*, boost::shared_ptr<boost::detail::tss_cleanup_function>, void*, bool)'  
bin.v2/libs/log/build/gcc-mingw-6.4/gentoorelease/build-no/pch-off/target-os-windows/threading-multi/named_scope.o:named_scope.cpp:(.text+0x823): undefined reference to `boost::detail::get_tss_data(void const*)'  
bin.v2/libs/log/build/gcc-mingw-6.4/gentoorelease/build-no/pch-off/target-os-windows/threading-multi/named_scope.o:named_scope.cpp:(.text+0x881): undefined reference to `boost::detail::get_tss_data(void const*)'  
bin.v2/libs/log/build/gcc-mingw-6.4/gentoorelease/build-no/pch-off/target-os-windows/threading-multi/named_scope.o:named_scope.cpp:(.text+0x8cf): undefined reference to `boost::detail::set_tss_data(void const*, boost::shared_ptr<boost::detail::tss_cleanup_function>, void*, bool)'  
bin.v2/libs/log/build/gcc-mingw-6.4/gentoorelease/build-no/pch-off/target-os-windows/threading-multi/named_scope.o:named_scope.cpp:(.text+0xa06): undefined reference to `boost::detail::get_tss_data(void const*)'  
bin.v2/libs/log/build/gcc-mingw-6.4/gentoorelease/build-no/pch-off/target-os-windows/threading-multi/named_scope.o:named_scope.cpp:(.text+0xa61): undefined reference to `boost::detail::get_tss_data(void const*)'  
bin.v2/libs/log/build/gcc-mingw-6.4/gentoorelease/build-no/pch-off/target-os-windows/threading-multi/named_scope.o:named_scope.cpp:(.text+0xaaf): undefined reference to `boost::detail::set_tss_data(void const*, boost::shared_ptr<boost::detail::tss_cleanup_function>, void*, bool)'  
bin.v2/libs/log/build/gcc-mingw-6.4/gentoorelease/build-no/pch-off/target-os-windows/threading-multi/named_scope.o:named_scope.cpp:(.text$_ZN5boost3log9v2_mt_nt510attributes11named_scope4implD1Ev[__ZN5boost3log9v2_mt_nt510attributes11named_scope4implD1Ev]+0x69): undefined reference to `boost::detail::set_tss_data(void const*, boost::shared_ptr<boost::detail::tss_cleanup_function>, void*, bool)'  
bin.v2/libs/log/build/gcc-mingw-6.4/gentoorelease/build-no/pch-off/target-os-windows/threading-multi/named_scope.o:named_scope.cpp:(.text$_ZN5boost3log9v2_mt_nt510attributes11named_scope4implD0Ev[__ZN5boost3log9v2_mt_nt510attributes11named_scope4implD0Ev]+0x69): undefined reference to `boost::detail::set_tss_data(void const*, boost::shared_ptr<boost::detail::tss_cleanup_function>, void*, bool)'  
bin.v2/libs/log/build/gcc-mingw-6.4/gentoorelease/build-no/pch-off/target-os-windows/threading-multi/named_scope.o:named_scope.cpp:(.text$_ZN5boost3log9v2_mt_nt510attributes11named_scope4impl9get_valueEv[__ZN5boost3log9v2_mt_nt510attributes11named_scope4impl9get_valueEv]+0x56): undefined reference to `boost::detail::get_tss_data(void const*)'  
bin.v2/libs/log/build/gcc-mingw-6.4/gentoorelease/build-no/pch-off/target-os-windows/threading-multi/named_scope.o:named_scope.cpp:(.text$_ZN5boost3log9v2_mt_nt510attributes11named_scope4impl9get_valueEv[__ZN5boost3log9v2_mt_nt510attributes11named_scope4impl9get_valueEv]+0xe1): undefined reference to `boost::detail::get_tss_data(void const*)'  
bin.v2/libs/log/build/gcc-mingw-6.4/gentoorelease/build-no/pch-off/target-os-windows/threading-multi/named_scope.o:named_scope.cpp:(.text$_ZN5boost3log9v2_mt_nt510attributes11named_scope4impl9get_valueEv[__ZN5boost3log9v2_mt_nt510attributes11named_scope4impl9get_valueEv]+0x141): undefined reference to `boost::detail::set_tss_data(void const*, boost::shared_ptr<boost::detail::tss_cleanup_function>, void*, bool)'  
bin.v2/libs/log/build/gcc-mingw-6.4/gentoorelease/build-no/pch-off/target-os-windows/threading-multi/thread_id.o:thread_id.cpp:(.text+0x22a): undefined reference to `boost::detail::add_thread_exit_function(boost::detail::thread_exit_function_base*)'  
bin.v2/libs/log/build/gcc-mingw-6.4/gentoorelease/build-no/pch-off/target-os-windows/threading-multi/once_block.o:once_block.cpp:(.text+0x20e7): undefined reference to `boost::this_thread::interruptible_wait(void*, boost::detail::timeout)'  
collect2: error: ld returned 1 exit status  
...removing bin.v2/libs/log/build/gcc-mingw-6.4/gentoorelease/build-no/pch-off/target-os-windows/threading-multi/libboost_log.dll.a  
...skipped <pstage/lib>libboost_log.dll.a for lack of <pbin.v2/libs/log/build/gcc-mingw-6.4/gentoorelease/build-no/pch-off/target-os-windows/threading-multi>libboost_log.dll.a...  
gcc.compile.c++ bin.v2/libs/serialization/build/gcc-mingw-6.4/gentoorelease/pch-off/target-os-windows/threading-multi/basic_serializer_map.o  
```  
  
The  pertinent info from the error log.

---

## Comment 2

> Username: Lastique  
> Created at: 2017-09-21 08:55:37 UTC  
> Url: https://github.com/boostorg/log/issues/41#issuecomment-331095275  

The missing symbols are from Boost.Thread which is missing in the linker command line. I also don't see it being built in the full log (it's supposed to be built before Boost.Log). I can see that the dependency on Boost.Thread is in place in Boost.Log Jamfile in boost_1_65_0.tar.bz2 and boost_1_65_1.tar.bz2, as well as git, so my only guess right now is that your source was modified. Is this the case? If so, try with the original source, preferably 1.65.1.  
  
PS: We have MinGW testers here: http://www.boost.org/development/tests/master/developer/log.html, they are not failing. This could be something specific to Gentoo, but I can't help with those specifics.

---

## Comment 3

> Username: hanetzer  
> Created at: 2017-09-21 09:20:23 UTC  
> Url: https://github.com/boostorg/log/issues/41#issuecomment-331101220  

@Lastique interesting. Well, if boostorg/thread#156 were to get fixed that would probably help, then.

---

## Comment 4

> Username: Lastique  
> Created at: 2017-09-21 10:02:41 UTC  
> Url: https://github.com/boostorg/log/issues/41#issuecomment-331111725  

I don't see how that would help. The missing symbols are from Boost.Thread itself (i.e. libboost_thread.a or something), not from libpthread.a. And since libboost_thread.a is not being built ant not present in the command line, changing the way Boost.Thread builds is unlikely to help.

---

## Comment 5

> Username: Lastique  
> Created at: 2017-09-21 10:09:03 UTC  
> Url: https://github.com/boostorg/log/issues/41#issuecomment-331113262  

Another idea is maybe the problem is caused by the `b2` version you're using. From the full log, it seems you're invoking the system `b2` instead of building the one shipped with Boost. There might have been some changes to Boost.Build that require the updated `b2`. Try running `bootstrap.sh` and using the `b2` compiled from the Boost sources.

---

## Comment 6

> Username: hanetzer  
> Created at: 2017-09-21 22:11:39 UTC  
> Url: https://github.com/boostorg/log/issues/41#issuecomment-331296173  

@Lastique system b2 is the same version as the tarball I'm attempting to compile. Using bootstrap.sh is not an acceptable solution in this situation as it would require massive hackage to the dev-libs/boost-1.65.0 ebuild. The reason Boost.Thread is not building (and therefore why boost.log is failing) is because with threadapi=posix boost expects libpthreadGC2.a to exist, instead of making use of libpthread.dll.a and so on.

---

## Comment 7

> Username: Lastique  
> Created at: 2017-09-21 22:31:28 UTC  
> Url: https://github.com/boostorg/log/issues/41#issuecomment-331299946  

> The reason Boost.Thread is not building (and therefore why boost.log is failing) is because with threadapi=posix boost expects libpthreadGC2.a to exist, instead of making use of libpthread.dll.a and so on.  
  
I'm not sure how that would be the reason to not build Boost.Thread yet still build Boost.Log that depends on it. IMO, either both should be compiled (and thus Boost.Thread may fail to build because of the missing libpthreadGC2.a) or neither should. I believe, that is the current behavior of Boost.Build (or if not - it should be).  
  
Anyway, this is not a problem with Boost.Log per se. I'm assuming that either the source code you're using was indeed modified or there is some issue with Boost.Build that results in attempt to build Boost.Log without Boost.Thread. In the latter case (i.e. if the source code is pristine) I suggest reporting a Boost.Build issue.
