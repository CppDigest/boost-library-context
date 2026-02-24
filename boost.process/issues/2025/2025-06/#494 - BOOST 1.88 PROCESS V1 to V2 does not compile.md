# #494 - BOOST 1.88 PROCESS V1 to V2 does not compile [Closed]

> Username: atidot3  
> Created at: 2025-06-13 08:17:55 UTC  
> Updated at: 2025-10-10 21:08:07 UTC  
> Closed at: 2025-06-13 09:33:31 UTC  
> Url: https://github.com/boostorg/process/issues/494  

GCC v => gcc (Debian 12.2.0-14+deb12u1) 12.2.0  
Boost Ver => set( Boost_Version 1.88.0 )  
Boost modules => set( Boost_Modules_To_Build system regex log date_time test coroutine program_options url charconv process)  
  
  
  
```  
/usr/bin/ld : CMakeFiles/httpx_tests.dir/test/server_tests.cpp.o : dans la fonction « boost::process::v2::detail::basic_process_handle_fd<boost::asio::any_io_executor>::running(int&, boost::system::error_code&) » :  
3rdParty/boost/include/boost/process/v2/detail/process_handle_fd.hpp:250 : référence indéfinie vers « boost::process::v2::detail::get_last_error() »  
/usr/bin/ld : CMakeFiles/httpx_tests.dir/test/server_tests.cpp.o : dans la fonction « boost::process::v2::detail::throw_error(boost::system::error_code const&, char const*) » :  
3rdParty/boost/include/boost/process/v2/detail/throw_error.hpp:26 : référence indéfinie vers « boost::process::v2::detail::do_throw_error(boost::system::error_code const&, char const*) »  
/usr/bin/ld : CMakeFiles/httpx_tests.dir/test/server_tests.cpp.o : dans la fonction « boost::process::v2::detail::basic_process_handle_fd<boost::asio::any_io_executor>::wait(int&, boost::system::error_code&) » :  
3rdParty/boost/include/boost/process/v2/detail/process_handle_fd.hpp:136 : référence indéfinie vers « boost::process::v2::detail::get_last_error() »  
/usr/bin/ld : CMakeFiles/httpx_tests.dir/test/server_tests.cpp.o : dans la fonction « boost::process::v2::posix::default_launcher::close_all_fds(boost::system::error_code&) » :  
3rdParty/boost/include/boost/process/v2/posix/default_launcher.hpp:429 : référence indéfinie vers « boost::process::v2::posix::detail::close_all(std::vector<int, std::allocator<int> > const&, boost::system::error_code&) »  
/usr/bin/ld : CMakeFiles/httpx_tests.dir/test/server_tests.cpp.o : dans la fonction « __static_initialization_and_destruction_0(int, int) [clone .constprop.0] » :  
3rdParty/boost/include/boost/process/v2/error.hpp:23 : référence indéfinie vers « boost::process::v2::error::get_utf8_category() »  
/usr/bin/ld : 3rdParty/boost/include/boost/process/v2/error.hpp:38 : référence indéfinie vers « boost::process::v2::error::get_exit_code_category() »  
/usr/bin/ld : CMakeFiles/httpx_tests.dir/test/server_tests.cpp.o : dans la fonction « __static_initialization_and_destruction_0(int, int) [clone .constprop.0] » :  
3rdParty/boost/include/boost/process/v2/shell.hpp:21 : référence indéfinie vers « boost::process::v2::get_shell_category() »  
/usr/bin/ld : CMakeFiles/httpx_tests.dir/test/server_tests.cpp.o : dans la fonction « boost::process::v2::detail::throw_error(boost::system::error_code const&, char const*) » :  
3rdParty/boost/include/boost/process/v2/detail/throw_error.hpp:26 : référence indéfinie vers « boost::process::v2::detail::do_throw_error(boost::system::error_code const&, char const*) »  
/usr/bin/ld : CMakeFiles/httpx_tests.dir/test/server_tests.cpp.o : dans la fonction « boost::process::v2::detail::process_io_binding<1>::process_io_binding<boost::asio::any_io_executor>(boost::asio::basic_readable_pipe<boost::asio::any_io_executor>&) » :  
3rdParty/boost/include/boost/process/v2/stdio.hpp:227 : référence indéfinie vers « boost::process::v2::detail::get_last_error() »  
/usr/bin/ld : CMakeFiles/httpx_tests.dir/test/server_tests.cpp.o : dans la fonction « boost::process::v2::environment::detail::is_executable(boost::filesystem::path const&, boost::system::error_code&) » :  
3rdParty/boost/include/boost/process/v2/detail/environment_posix.hpp:81 : référence indéfinie vers « boost::process::v2::environment::detail::has_x_access(char const*) »  
/usr/bin/ld : CMakeFiles/httpx_tests.dir/test/server_tests.cpp.o : dans la fonction « boost::process::v2::detail::throw_error(boost::system::error_code const&, char const*) » :  
3rdParty/boost/include/boost/process/v2/detail/throw_error.hpp:26 : référence indéfinie vers « boost::process::v2::detail::do_throw_error(boost::system::error_code const&, char const*) »  
/usr/bin/ld : 3rdParty/boost/include/boost/process/v2/detail/throw_error.hpp:26 : référence indéfinie vers « boost::process::v2::detail::do_throw_error(boost::system::error_code const&, char const*) »  
collect2: error: ld returned 1 exit status  
make[2]: *** [lib_httpx/CMakeFiles/httpx_tests.dir/build.make:327 : lib_httpx/httpx_tests] Erreur 1  
make[1]: *** [CMakeFiles/Makefile2:641 : lib_httpx/CMakeFiles/httpx_tests.dir/all] Erreur 2  
make: *** [Makefile:91 : all] Erreur 2  
  
  
  
```

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2025-06-13 08:18:50 UTC  
> Url: https://github.com/boostorg/process/issues/494#issuecomment-2969501474  

You need to link against boost.process, too. It's not header only anymore.

---

## Comment 2

> Username: atidot3  
> Created at: 2025-06-13 09:33:31 UTC  
> Url: https://github.com/boostorg/process/issues/494#issuecomment-2969731647  

Well well might have guess, thanks good sir :)

---

## Comment 3

> Username: marek22k  
> Created at: 2025-10-10 21:08:07 UTC  
> Url: https://github.com/boostorg/process/issues/494#issuecomment-3392312248  

Is there a Debian package for this? https://pkgs.org/search/?q=libboost-process states that the package is only available in sid. Is there a workaround for Trixie?
