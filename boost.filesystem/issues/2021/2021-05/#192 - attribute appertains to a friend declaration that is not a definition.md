# #192 - attribute appertains to a friend declaration that is not a definition [Closed]

> Username: BenniProbst  
> Created at: 2021-05-31 17:11:32 UTC  
> Updated at: 2021-05-31 17:24:03 UTC  
> Closed at: 2021-05-31 17:24:03 UTC  
> Url: https://github.com/boostorg/filesystem/issues/192  

#include <boost/filesystem.hpp>  
#include <boost/thread.hpp>  
  
void corefreq(boost::filesystem::path current_work) {  
	root_work_path =current_work;  
	boost::filesystem::path corefreq = current_work.append(boost::filesystem::system_complete("./CoreFreq"));//#15 main.cpp  
	std::cout << "Deleting old CoreFreq" << std::endl;  
	if (is_directory(corefreq)) {  
		(void)boost::filesystem::remove_all(corefreq);  
	}  
	std::string command = "cd " + corefreq.string();  
	(void)std::system(command.c_str());  
	std::cout << "Make sure make is updated." << std::endl;  
	(void)std::system("apt-get update -y");  
	(void)std::system("apt-get upgrade -y");  
{...}  
}  
  
int main(int argc, char *argv[]) {  
	//prepare CoreFreq module  
	try {  
		corefreq(boost::filesystem::system_complete(argv[0]));  
	}  
	catch (std::exception &e) {  
		std::cout << "CPU module install exception: " << e.what() << std::endl;  
		return 1;  
	}  
{...}  
}  
  
throws:  
====================[ Build | Kodierungstheorie | Debug ]=======================  
/usr/local/bin/cmake --build /home/benjamin-elias/CLionProjects/Kodierungstheorie/cmake-build-debug --target Kodierungstheorie -- -j 6  
Scanning dependencies of target Kodierungstheorie  
[ 16%] Building CXX object CMakeFiles/Kodierungstheorie.dir/main.cpp.o  
In Datei, eingebunden von /home/benjamin-elias/Downloads/boost_1_76_0/boost/thread/thread_only.hpp:22,  
                 von /home/benjamin-elias/Downloads/boost_1_76_0/boost/thread/thread.hpp:12,  
                 von /home/benjamin-elias/Downloads/boost_1_76_0/boost/thread.hpp:13,  
                 von /home/benjamin-elias/CLionProjects/Kodierungstheorie/main.cpp:2:  
/home/benjamin-elias/Downloads/boost_1_76_0/boost/thread/detail/thread.hpp:641:37: Fehler: attribute appertains to a friend declaration that is not a definition  
  641 |         friend id BOOST_THREAD_DECL this_thread::get_id() BOOST_NOEXCEPT;  
      |                                     ^~~~~~~~~~~  
In Datei, eingebunden von /usr/local/gcc-11.1/include/c++/12.0.0/string:55,  
                 von /home/benjamin-elias/Downloads/boost_1_76_0/boost/system/detail/error_category.hpp:16,  
                 von /home/benjamin-elias/Downloads/boost_1_76_0/boost/system/detail/error_code.hpp:13,  
                 von /home/benjamin-elias/Downloads/boost_1_76_0/boost/system/error_code.hpp:13,  
                 von /home/benjamin-elias/Downloads/boost_1_76_0/boost/filesystem/path_traits.hpp:22,  
                 von /home/benjamin-elias/Downloads/boost_1_76_0/boost/filesystem/path.hpp:26,  
                 von /home/benjamin-elias/Downloads/boost_1_76_0/boost/filesystem.hpp:16,  
                 von /home/benjamin-elias/CLionProjects/Kodierungstheorie/main.cpp:1:  
/usr/local/gcc-11.1/include/c++/12.0.0/bits/basic_string.h: In Instanziierung von »static void std::__cxx11::basic_string<_CharT, _Traits, _Alloc>::_S_copy_chars(_CharT*, _Iterator, _Iterator) [with _Iterator = boost::filesystem::path::iterator; _CharT = char; _Traits = std::char_traits<char>; _Alloc = std::allocator<char>]«:  
/usr/local/gcc-11.1/include/c++/12.0.0/bits/basic_string.tcc:225:25:   erfordert durch »void std::__cxx11::basic_string<_CharT, _Traits, _Alloc>::_M_construct(_InIterator, _InIterator, std::forward_iterator_tag) [with _FwdIterator = boost::filesystem::path::iterator; _CharT = char; _Traits = std::char_traits<char>; _Alloc = std::allocator<char>]«  
/usr/local/gcc-11.1/include/c++/12.0.0/bits/basic_string.h:254:23:   erfordert durch »void std::__cxx11::basic_string<_CharT, _Traits, _Alloc>::_M_construct_aux(_InIterator, _InIterator, std::__false_type) [with _InIterator = boost::filesystem::path::iterator; _CharT = char; _Traits = std::char_traits<char>; _Alloc = std::allocator<char>]«  
/usr/local/gcc-11.1/include/c++/12.0.0/bits/basic_string.h:273:20:   erfordert durch »void std::__cxx11::basic_string<_CharT, _Traits, _Alloc>::_M_construct(_InIterator, _InIterator) [with _InIterator = boost::filesystem::path::iterator; _CharT = char; _Traits = std::char_traits<char>; _Alloc = std::allocator<char>]«  
/usr/local/gcc-11.1/include/c++/12.0.0/bits/basic_string.h:640:16:   erfordert durch »std::__cxx11::basic_string<_CharT, _Traits, _Alloc>::basic_string(_InputIterator, _InputIterator, const _Alloc&) [with _InputIterator = boost::filesystem::path::iterator; <template-parameter-2-2> = void; _CharT = char; _Traits = std::char_traits<char>; _Alloc = std::allocator<char>]«  
/home/benjamin-elias/Downloads/boost_1_76_0/boost/filesystem/path_traits.hpp:321:57:   erfordert durch »typename boost::disable_if<boost::is_array<Container>, void>::type boost::filesystem::path_traits::dispatch(const Container&, U&) [with Container = boost::filesystem::path; U = std::__cxx11::basic_string<char>; typename boost::disable_if<boost::is_array<Container>, void>::type = void]«  
/home/benjamin-elias/Downloads/boost_1_76_0/boost/filesystem/path.hpp:938:26:   erfordert durch »boost::filesystem::path& boost::filesystem::path::append(const Source&) [with Source = boost::filesystem::path]«  
/home/benjamin-elias/CLionProjects/Kodierungstheorie/main.cpp:15:56:   von hier erfordert  
/usr/local/gcc-11.1/include/c++/12.0.0/bits/basic_string.h:386:32: Fehler: keine passende Funktion für Aufruf von »std::char_traits<char>::assign(char&, const boost::filesystem::path&)«  
  386 |             traits_type::assign(*__p, *__k1); // These types are off.  
      |             ~~~~~~~~~~~~~~~~~~~^~~~~~~~~~~~~  
In Datei, eingebunden von /usr/local/gcc-11.1/include/c++/12.0.0/string:40,  
                 von /home/benjamin-elias/Downloads/boost_1_76_0/boost/system/detail/error_category.hpp:16,  
                 von /home/benjamin-elias/Downloads/boost_1_76_0/boost/system/detail/error_code.hpp:13,  
                 von /home/benjamin-elias/Downloads/boost_1_76_0/boost/system/error_code.hpp:13,  
                 von /home/benjamin-elias/Downloads/boost_1_76_0/boost/filesystem/path_traits.hpp:22,  
                 von /home/benjamin-elias/Downloads/boost_1_76_0/boost/filesystem/path.hpp:26,  
                 von /home/benjamin-elias/Downloads/boost_1_76_0/boost/filesystem.hpp:16,  
                 von /home/benjamin-elias/CLionProjects/Kodierungstheorie/main.cpp:1:  
/usr/local/gcc-11.1/include/c++/12.0.0/bits/char_traits.h:328:7: Anmerkung: Kandidat: »static constexpr void std::char_traits<char>::assign(std::char_traits<char>::char_type&, const char_type&)«  
  328 |       assign(char_type& __c1, const char_type& __c2) _GLIBCXX_NOEXCEPT  
      |       ^~~~~~  
/usr/local/gcc-11.1/include/c++/12.0.0/bits/char_traits.h:328:48: Anmerkung:   keine bekannte Umwandlung für Argument 2 von »const boost::filesystem::path« nach »const char_type&« {aka »const char&«}  
  328 |       assign(char_type& __c1, const char_type& __c2) _GLIBCXX_NOEXCEPT  
      |                               ~~~~~~~~~~~~~~~~~^~~~  
/usr/local/gcc-11.1/include/c++/12.0.0/bits/char_traits.h:413:7: Anmerkung: Kandidat: »static constexpr std::char_traits<char>::char_type* std::char_traits<char>::assign(std::char_traits<char>::char_type*, std::size_t, std::char_traits<char>::char_type)«  
  413 |       assign(char_type* __s, size_t __n, char_type __a)  
      |       ^~~~~~  
/usr/local/gcc-11.1/include/c++/12.0.0/bits/char_traits.h:413:7: Anmerkung:   Kandidat erwartet 3 Argumente, 2 angegeben  
make[3]: *** [CMakeFiles/Kodierungstheorie.dir/build.make:72: CMakeFiles/Kodierungstheorie.dir/main.cpp.o] Fehler 1  
make[2]: *** [CMakeFiles/Makefile2:85: CMakeFiles/Kodierungstheorie.dir/all] Fehler 2  
make[1]: *** [CMakeFiles/Makefile2:92: CMakeFiles/Kodierungstheorie.dir/rule] Fehler 2  
make: *** [Makefile:124: Kodierungstheorie] Fehler 2

---

## Comment 1

> Username: Lastique  
> Created at: 2021-05-31 17:24:03 UTC  
> Url: https://github.com/boostorg/filesystem/issues/192#issuecomment-851607085  

The error points to a Boost.Thread header, not Boost.Filesystem. Please, report it to [Boost.Thread](https://github.com/boostorg/thread/issues).
