# #37 - segfault on locale "hy_AM.ARMSCII-8" on FreeBSD [Closed]

> Username: schorsch1976  
> Created at: 2018-12-21 06:13:27 UTC  
> Updated at: 2018-12-21 10:37:10 UTC  
> Closed at: 2018-12-21 10:37:10 UTC  
> Url: https://github.com/boostorg/locale/issues/37  

// build with  
	// clang --std=c++17 bug.cpp -O0 -g  -I /usr/local/include -lc++ -L/usr/local/lib/ -lboost_locale  
	// freebsd-version  11.2-RELEASE-p1  
	// clang FreeBSD clang version 6.0.0 (tags/RELEASE_600/final 326565) (based on LLVM 6.0.0)  
  
	#include <boost/locale.hpp>  
  
	int main(int argc, char** argv)  
	{  
		// Set global localization backend  
		using namespace boost::locale;  
		localization_backend_manager my = localization_backend_manager::global();  
		my.select("icu");  
		localization_backend_manager::global(my);  
  
		boost::locale::generator gen;  
		std::locale loc{ gen("hy_AM.ARMSCII-8") };  
		std::locale::global(loc);  
	}  
  
This segfaults:  
  
		Program received signal SIGABRT, Aborted.  
	0x00000008012c147a in thr_kill () from /lib/libc.so.7  
	(gdb) bt  
	#0  0x00000008012c147a in thr_kill () from /lib/libc.so.7  
	#1  0x00000008012c1444 in raise () from /lib/libc.so.7  
	#2  0x00000008012c13b9 in abort () from /lib/libc.so.7  
	#3  0x0000000800d5cb2b in boost::locale::impl_icu::icu_std_converter<char, 1>::uconv::uconv(std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> > const&, boost::locale::impl_icu::cpcvt_type) () from /usr/local/lib/libboost_locale.so.1.68.0  
	#4  0x0000000800d5c914 in boost::locale::impl_icu::icu_std_converter<char, 1>::icu_std_converter(std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> >, boost::locale::impl_icu::cpcvt_type) () from /usr/local/lib/libboost_locale.so.1.68.0  
	#5  0x0000000800d834cf in boost::locale::impl_icu::collate_impl<char>::collate_impl(boost::locale::impl_icu::cdata const&) ()  
	from /usr/local/lib/libboost_locale.so.1.68.0  
	#6  0x0000000800d83316 in boost::locale::impl_icu::create_collate(std::__1::locale const&, boost::locale::impl_icu::cdata const&, unsigned int) ()  
	from /usr/local/lib/libboost_locale.so.1.68.0  
	#7  0x0000000800d8e10f in boost::locale::impl_icu::icu_localization_backend::install(std::__1::locale const&, unsigned int, unsigned int) ()  
	from /usr/local/lib/libboost_locale.so.1.68.0  
	#8  0x0000000800d69245 in boost::locale::localization_backend_manager::impl::actual_backend::install(std::__1::locale const&, unsigned int, unsigned int)  
		() from /usr/local/lib/libboost_locale.so.1.68.0  
	#9  0x0000000800d658d7 in boost::locale::generator::generate(std::__1::locale const&, std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> > const&) const () from /usr/local/lib/libboost_locale.so.1.68.0  
	#10 0x0000000800d6571d in boost::locale::generator::generate(std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> > const&) const () from /usr/local/lib/libboost_locale.so.1.68.0  
	#11 0x00000000004011c4 in boost::locale::generator::operator() (this=0x7fffffffe1f0, id=...) at /usr/local/include/boost/locale/generator.hpp:203  
	#12 0x0000000000401093 in main (argc=1, argv=0x7fffffffe2f8) at bug.cpp:17

---

## Comment 1

> Username: artyom-beilis  
> Created at: 2018-12-21 07:36:29 UTC  
> Url: https://github.com/boostorg/locale/issues/37#issuecomment-449292393  

Have you tried catching an exception?

---

## Comment 2

> Username: schorsch1976  
> Created at: 2018-12-21 09:42:48 UTC  
> Url: https://github.com/boostorg/locale/issues/37#issuecomment-449334747  

Ok, i got this issue on the test on #35 #36 . There is an exception. Ok, you can close this bug #37.
